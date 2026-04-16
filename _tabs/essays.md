---
layout: page
title: Essays
icon: fas fa-satellite-dish
order: 3
---

# Essays

{% assign essay_posts = site.posts | where_exp: "post", "post.categories contains 'Essay' or post.categories contains 'Cosmology' or post.categories contains 'History'" | sort: "date" | reverse %}

{% if essay_posts.size > 0 %}
{% for post in essay_posts %}
- [{{ post.title }}]({{ post.url | relative_url }}) — {{ post.date | date: "%Y-%m-%d" }}
{% endfor %}
{% endif %}
