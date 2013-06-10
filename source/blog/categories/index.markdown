---
layout: page
title: "Categories"
comments: false
sharing: false
footer: false
---


<ul>
{% for item in site.categories %}
    <li><a href="/blog/categories/{{ item[0] }}/">{{ item[0] }}</a> [ {{ item[1].size }} ]</li>
{% endfor %}
</ul>