---
layout: post
title: 分类
---


{% assign sorted_categories = site.categories | sort %}
{% for category in sorted_categories %}
<h2 id="{{ category | first }}">{{ category | first }}</h2>
<ul >
{% assign sorted_posts = category.last | sort: 'title','last' %}
{% for post in sorted_posts %}
<li >
<a href="{{ site.url }}{{ site.baseurl }}/{{ post.url }}">{{ post.title }}</a>
</li>
{% endfor %}
</ul>
{% endfor %}
