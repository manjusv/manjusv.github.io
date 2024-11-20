---
layout: default
title: "All Posts"
permalink: /posts/
---

# All Posts

{% for post in site.posts %}
- [{{ post.title }}]({{ post.url }})
- {{ post.category }}
- {{ post.tag }}
    {% for hash in category_hashes %}
        {% assign keyValue = hash | split: '#' %}
        {% capture category_word %}{{ keyValue[1] | strip_newlines }}{% endcapture %}
        <a href="{{ category_word | slugify | prepend: path_type | prepend: site.category_archive.path | relative_url }}" class="page__taxonomy-item" rel="tag">{{ category_word }}</a>{% unless forloop.last %}<span class="sep">, </span>{% endunless %}
    {% endfor %}
{% endfor %}
