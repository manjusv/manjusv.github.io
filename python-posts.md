---
layout: default
title: "Python Posts"
permalink: /python-posts/
---

# Python Posts

{% for post in site.posts %}
{% if post.categories contains "python" %}
- [{{ post.title }}]({{ post.url }})
{% endif %}
{% endfor %}