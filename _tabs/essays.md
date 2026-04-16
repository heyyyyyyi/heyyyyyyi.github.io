---
layout: page
title: Essays
icon: fas fa-satellite-dish
order: 3
---

# Essays / 随笔

这个栏目留给更宽一点的话题：宇宙学、历史、文化观察、慢一点的研究笔记。

建议新文章使用这些分类方式：

- `categories: [Essay]`
- `categories: [Essay, Cosmology]`
- `categories: [Essay, History]`

## Latest Essay Notes

{% assign essay_posts = site.posts | where_exp: "post", "post.categories contains 'Essay' or post.categories contains 'Cosmology' or post.categories contains 'History'" | sort: "date" | reverse %}

{% if essay_posts.size > 0 %}
{% for post in essay_posts %}
- [{{ post.title }}]({{ post.url | relative_url }}) — {{ post.date | date: "%Y-%m-%d" }}
{% endfor %}
{% else %}
目前还没有归档到这个栏目里的文章。新文章加上 `Essay` / `Cosmology` / `History` 分类后，这里会自动更新。
{% endif %}
