---
layout: page
icon: fas fa-book-open
order: 3
title: Reading
---

{% assign en_notes = site.reading | where: "lang", "en" | sort: "date" | reverse %}
{% assign zh_notes = site.reading | where: "lang", "zh" | sort: "date" | reverse %}

<div class="lang-columns">
  <section class="lang-col">
    <h2 class="lang-heading">English</h2>
    <ul class="reading-list">
      {% for note in en_notes %}
        <li class="reading-item">
          <a class="reading-link" href="{{ note.url | relative_url }}">{{ note.title }}</a>
          <div class="reading-meta">
            <span class="reading-date">{{ note.date | date: "%Y-%m-%d" }}</span>
            {% if note.source_type %}<span class="reading-type">{{ note.source_type }}</span>{% endif %}
            {% if note.source %}<a class="reading-source" href="{{ note.source }}" target="_blank" rel="noopener">source ↗</a>{% endif %}
          </div>
        </li>
      {% endfor %}
    </ul>
  </section>

  <section class="lang-col">
    <h2 class="lang-heading">中文</h2>
    <ul class="reading-list">
      {% for note in zh_notes %}
        <li class="reading-item">
          <a class="reading-link" href="{{ note.url | relative_url }}">{{ note.title }}</a>
          <div class="reading-meta">
            <span class="reading-date">{{ note.date | date: "%Y-%m-%d" }}</span>
            {% if note.source_type %}<span class="reading-type">{{ note.source_type }}</span>{% endif %}
            {% if note.source %}<a class="reading-source" href="{{ note.source }}" target="_blank" rel="noopener">原文 ↗</a>{% endif %}
          </div>
        </li>
      {% endfor %}
    </ul>
  </section>
</div>
