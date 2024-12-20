---
layout: default
author_profile: true
title: "Data Engineering Posts"
permalink: /data-engineering-posts/
---

# Data Engineering Posts

{% for post in site.posts %}
{% if post.categories contains "data engineering" %}
- [{{ post.title }}]({{ post.url }})
{% endif %}
{% endfor %}