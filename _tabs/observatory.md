---
layout: page
title: Observatory
icon: fas fa-chart-area
order: 4
---

# Observatory / 访问观察站

这个页面专门给站点统计和访客来源准备。

{% if site.analytics.umami.id %}
## Current Status

- Umami tracking appears to be configured.
- 如果你已经在 Umami 后台接入这个站点，国家/地区来源会在 Umami 仪表盘里可见。
{% elsif site.analytics.cloudflare.id %}
## Current Status

- Cloudflare Web Analytics appears to be configured.
- Cloudflare 更适合看总体流量、来源趋势和国家维度概览。
{% else %}
## Current Status

- 还没有检测到统计 ID。
- 你可以直接在 `_config.yml` 里填写 `analytics.umami` 或 `analytics.cloudflare`。
{% endif %}

## Recommended Setup

### Option 1: Umami

- 适合你这种静态博客。
- 能看页面访问量和访问国家/地区。
- Chirpy 主题原生支持，在 `_config.yml` 中填写：

```yml
analytics:
  umami:
    id: YOUR_UMAMI_WEBSITE_ID
    domain: your-umami-domain.com
```

### Option 2: Cloudflare Web Analytics

- 接入更简单。
- 适合看总体访问趋势和国家分布。
- 在 `_config.yml` 中填写：

```yml
analytics:
  cloudflare:
    id: YOUR_CLOUDFLARE_TOKEN
```

## Making Essays Discoverable

如果以后你发宇宙学或历史随笔，建议给文章加上：

- `categories: [Essay]`
- `categories: [Essay, Cosmology]`
- `categories: [Essay, History]`

这样首页和 `Essays` 页面都会自动聚合它们。
