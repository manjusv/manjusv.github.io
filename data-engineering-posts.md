---
layout: default
author_profile: true
title: "Spark Posts"
permalink: /data-engineering-posts/
---

# Spark Posts

{% for post in site.posts %}
{% if post.categories contains "spark" %}
- [{{ post.title }}]({{ post.url }})
{% endif %}
{% endfor %}