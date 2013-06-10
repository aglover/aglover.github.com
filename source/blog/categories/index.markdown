---
layout: page
title: "Categories"
comments: false
sharing: false
footer: false
---
<!-- http://vigodome.com/blog/2011/12/22/show-categories-and-post-count-in-octopress/ -->
<!-- http://gangmax.me/blog/2012/05/04/add-about-page-in-octopress/ -->

<ul>
{% for item in site.categories %}
    <li><a href="/blog/categories/{{ item[0] }}/">{{ item[0] }}</a> [ {{ item[1].size }} ]</li>
{% endfor %}
</ul>