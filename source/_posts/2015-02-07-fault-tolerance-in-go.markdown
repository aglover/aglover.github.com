---
layout: post
title: "Fault tolerance in Go"
date: 2015-02-07 16:29
comments: true
categories: [Go, Java]
---

{% img right /images/mine/hystrix.png %}In distributed systems, failure is inevitable. Eventually, some service will become bogged down and consequently won't respond quickly enough or, worse, a service will simply die. Services relying on a degraded (or dead!) service will naturally become affected and potentially cascade instability throughout the system, unless all services are properly built with isolation and in mind. 

<!-- more --> 

[Netflix experienced the vexation distributed system failures inflict](http://techblog.netflix.com/2011/12/making-netflix-api-more-resilient.html) and developed [Hystrix](https://github.com/Netflix/Hystrix) as a sophisticated implementation of the [Circuit Breaker pattern](http://martinfowler.com/bliki/CircuitBreaker.html). Briefly, Hystrix is 

{% blockquote Github https://github.com/Netflix/Hystrix/wiki What is Hystrix? %}
a latency and fault tolerance library designed to isolate points of access to remote systems, services and 3rd party libraries, stop cascading failure and enable resilience in complex distributed systems where failure is inevitable.
{% endblockquote %}

Indeed, Hystrix is leveraged heavily at Netflix and 

{% blockquote Github https://github.com/Netflix/Hystrix/wiki What is Hystrix? %}
tens of billions of thread-isolated and hundreds of billions of semaphore-isolated calls are executed via Hystrix every day at Netflix and a dramatic improvement in uptime and resilience has been achieved through its use.
{% endblockquote %}

While [Hystrix](http://techblog.netflix.com/2012/11/hystrix.html) is [implemented in Java](https://github.com/Netflix/Hystrix/wiki/Getting-Started), alternate implementations are available. In fact, if you're building a service in [Go](https://golang.org/), then you'll most definitely want to take a look at [hystrix-go](https://github.com/afex/hystrix-go), which as its name implies, is a fairly easy to use Go implementation of Hystrix. 

As an example, here's some Go code that queries a 3rd party service (in this case, JIRA, in order to fetch open issues):

``` go Simple function to fetch open issues
func retrieveOpenIssues(userConfig UserDefinedConfig) JiraSearchResponse {
	url := fmt.Sprintf("%s/rest/api/2/search", userConfig.Host)
	query := fmt.Sprintf("project = %s AND resolution = Unresolved ORDER BY priority DESC", userConfig.Project)
	fields := []string{"summary", "key", "status", "assignee", "description", "issuetype", "created"}
	jsonStr, _ := json.Marshal(JiraSearchRequest{query, 0, fields})
	responseChannel := make(chan JiraSearchResponse)
	go func() {
		body := httpRequest(url, "POST", jsonStr, userConfig)
		var searchRes JiraSearchResponse
		json.Unmarshal(body, &searchRes)
		responseChannel <- searchRes
	}()
	return <-responseChannel
} 
```

This code works well enough _until JIRA starts to have issues_, in which case, a timeout will eventually create a rather nasty error within the calling code. In fact, Go's [built in panic](http://blog.golang.org/defer-panic-and-recover) will occur:

``` bash Go panics!
panic: Post https://jira.acme.com/rest/api/2/search: dial tcp 94.144.0.13:443: i/o timeout

goroutine 24 [running]:
runtime.panic(0x325620, 0xc208024bd0)
	/usr/local/go/src/pkg/runtime/panic.c:279 +0xf5
...
```

Of course, I could naturally build in some guard-like code to handle these errors, or I could use a framework modeled after Hystrix to do that for me. As [I'm lazy](http://thediscoblog.com/blog/2014/03/29/custom-git-commands-in-3-steps/), I tend to want to leverage other people's expertise and hystrix-go is no exception! 

Naturally, you'll first need to import hystrix-go: 


``` go import hystrix-go
import "github.com/afex/hystrix-go/hystrix"
```

hystrix-go is super simple to implement in your code -- you simply implement your logic inside a `hystrix.Go` function (which [internally creates](https://github.com/afex/hystrix-go/blob/master/hystrix/hystrix.go#L18) a [goroutine](https://golang.org/doc/effective_go.html#goroutines)). What's more, you create a _fallback_ function, which is invoked in an error condition ([such as a timeout, etc](https://github.com/Netflix/Hystrix/wiki/How-it-Works)). 


``` go hystrix-go is super simple to use
hystrix.Go("some command", func() error {
    // normal path code
    return nil
}, func(err error) error {
    // do this when errors occur 
    return nil
})
```

Note, the `hystrix.Go` function takes three arguments, the last two being [anonymous functions](https://gobyexample.com/closures): the first one being your normal logic, and the last being your desired fallback logic. You can naturally use a [Go Channel](https://gobyexample.com/channels) to receive data back from either function. 

The `string` first argument is a key, which can be matched with a unique configuration. For instance, you can set up individual timeouts and even error thresholds before a fallback kicks in. 


``` go hystrix-go is also easy to configure
hystrix.ConfigureCommand("unique_command", hystrix.CommandConfig{
    Timeout:               1000,
    MaxConcurrentRequests: 100,
    ErrorPercentThreshold: 25,
})
```

In the case above, timeouts are in milliseconds; consequently, the command `unique_command` will trigger a fallback condition after 1 second.  The library is well tested and I recommend [reading some of its tests](https://github.com/afex/hystrix-go/blob/master/hystrix/hystrix_test.go) to see various fallback conditions triggered. 

Armed with the knowledge of how to add a fallback and configure any conditions associated with it, it's super easy to incorporate legitimate fault tolerance in any go code. For instance, I can take that JIRA invoking code and wrap it inside a `hystrix.Go` function like so:

``` go Now with more Hystrix! 
func retrieveOpenIssues(userConfig UserDefinedConfig) JiraSearchResponse {
	url := fmt.Sprintf("%s/rest/api/2/search", userConfig.Host)
	query := fmt.Sprintf("project = %s AND resolution = Unresolved ORDER BY priority DESC", userConfig.Project)
	fields := []string{"summary", "key", "status", "assignee", "description", "issuetype", "created"}
	jsonStr, _ := json.Marshal(JiraSearchRequest{query, 0, fields})
	responseChannel := make(chan JiraSearchResponse)
	hystrix.ConfigureCommand("Get all Issues", hystrix.CommandConfig{Timeout: 2000})
	hystrix.Go("Get all Issues", func() error {
		body := httpRequest(url, "POST", jsonStr, userConfig)
		var searchRes JiraSearchResponse
		json.Unmarshal(body, &searchRes)
		responseChannel <- searchRes
		return nil
	}, func(err error) error {
		var searchRes JiraSearchResponse
		responseChannel <- searchRes
		return nil
	})
	return <-responseChannel
}
```

In this case, I've also specified that the "Get all Issues" keyed function will trigger a timeout after 2 seconds. My normal logic leveraging an HTTP `GET` is placed inside the first anonymous function and my fallback logic is placed in the second. This fallback logic simply returns an empty response so as not to completely affect downstream services relying on some sort of response. That is, if JIRA is slow, down, or just not responding, rather than a panic being generated, an empty response is returned. This ostensibly prohibits a cascading error chain. 

Of course, my fallback code could do other logic, if deemed necessary; what's more, my fallback code can detect the type of error generated. For instance, my fallback code could react differently in the case of a timeout as opposed to a concurrency issue. The `error` object passed into the fallback function can be queried easily enough: `if err.Error() == "max concurrency"`.  

[Netflix has seen success with Hystrix](http://techblog.netflix.com/2012/02/fault-tolerance-in-high-volume.html) in a large volume, highly distributed system. If you want to build tolerance into any Go services, then I can't recommend hystrix-go enough: it's super easy to set up and use. 