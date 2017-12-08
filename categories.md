---
layout: post
title: 分类
---


{% assign sorted_categories = site.categories | sort %}
{% for category in sorted_categories %}
<h2 id="{{ category | first }}">{{ category | first }}</h2>
<ul >
{% for post in category.last %}
<li >
<a href="{{ site.url }}{{ site.baseurl }}/{{ post.url }}">{{ post.title }}</a>
</li>
{% endfor %}
</ul>
{% endfor %}
