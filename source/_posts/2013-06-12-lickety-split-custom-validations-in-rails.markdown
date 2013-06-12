---
layout: post
title: "Lickety-split custom validations in Rails"
date: 2013-06-12 13:19
comments: true
categories: [Ruby, Rails]
---
Have a highly specific, yet custom validation for a particular field on one of your [Rails](http://thediscoblog.com/blog/categories/rails/) model objects? Don't want to create a `ActiveModel::Validator` type? Not a problem! 

You can just as easily [create a method](http://guides.rubyonrails.org/active_record_validations_callbacks.html) that can be invoked as part of the validation process. For example, imagine a field dubbed `uri` in some model object; this field must begin with a protocol (i.e. http or https). You can create a validation method like so:

``` ruby Custom validator
def uri_should_start_with_protocol
  if !uri.start_with?('http://') && !uri.start_with?('https://')
    errors.add(:uri, 'Web Address should start with http:// or https://')
  end
end
```
This method resides in your model. You can then register this method as a validation for your model like so:

``` ruby Wiring the validation
validate :uri_should_start_with_protocol
```

Now if the `uri` field doesn't contain http or https, `model.save` will return `false`. Done! 
