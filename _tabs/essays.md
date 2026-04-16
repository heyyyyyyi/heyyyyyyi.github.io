---
layout: page
title: Essays
icon: fas fa-satellite-dish
order: 8
---

# Essays

{% assign essay_posts = site.posts | where_exp: "post", "post.categories contains 'Essay' or post.categories contains 'Cosmology' or post.categories contains 'History'" | sort: "date" | reverse %}

{% if essay_posts.size > 0 %}
<div class="post-radar-grid">
{% for post in essay_posts %}
  <article class="post-radar-card">
    <a href="{{ post.url | relative_url }}">
      <h3 class="post-radar-title">{{ post.title }}</h3>
      <p class="post-radar-meta">{{ post.date | date: "%Y-%m-%d" }}{% if post.lang %} · {{ post.lang | upcase }}{% endif %}</p>
      <p class="post-radar-excerpt">{{ post.excerpt | strip_html | truncate: 140 }}</p>
    </a>
  </article>
{% endfor %}
</div>
{% endif %}
