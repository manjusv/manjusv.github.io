---
layout: default
author_profile: true
title: "Django Posts"
permalink: /django-posts/
---

# Django Posts

{% for post in site.posts %}
{% if post.categories contains "django" %}
- [{{ post.title }}]({{ post.url }})
{% endif %}
{% endfor %}