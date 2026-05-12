---
layout: page
icon: fas fa-stream
order: 1
title: Posts
---

{% assign en_posts = site.posts | where: "lang", "en" | sort: "date" | reverse %}
{% assign zh_posts = site.posts | where: "lang", "zh" | sort: "date" | reverse %}

<div class="lang-columns">
  <section class="lang-col">
    <h2 class="lang-heading">English</h2>
    <ul class="lang-list">
      {% for post in en_posts %}
        <li>
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
          <span class="lang-meta">{{ post.date | date: "%Y-%m-%d" }}</span>
        </li>
      {% endfor %}
    </ul>
  </section>

  <section class="lang-col">
    <h2 class="lang-heading">中文</h2>
    <ul class="lang-list">
      {% for post in zh_posts %}
        <li>
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
          <span class="lang-meta">{{ post.date | date: "%Y-%m-%d" }}</span>
        </li>
      {% endfor %}
    </ul>
  </section>
</div>

