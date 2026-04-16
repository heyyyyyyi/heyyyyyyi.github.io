---
layout: page
title: English
icon: fas fa-language
order: 7
---

## English Posts

{% assign en_posts = site.posts | where: "lang", "en" %}

{% for post in en_posts %}
- [{{ post.title }}]({{ post.url | relative_url }}) — {{ post.date | date: "%Y-%m-%d" }}
{% endfor %}
