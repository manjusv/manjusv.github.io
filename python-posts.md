---
layout: default
title: "Python Posts"
permalink: /python-posts/
---

# Python Posts

{% for post in site.posts %}
{% if "python" in post.categories %}
- [{{ post.title }}]({{ post.url }})
{% endif %}
{% endfor %}