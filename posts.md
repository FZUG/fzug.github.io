---
layout: page
title: 全部文章
permalink: /posts/
---

{%- assign date_format = site.minima.date_format | default: "%Y-%m-%d" -%}

<ul class="post-list">
{%- for post in site.posts -%}
  <li class="post-item">
    <span class="post-meta">{{ post.date | date: date_format }}</span>
    <h3>
      <a class="post-link" href="{{ post.url | relative_url }}">
        {{ post.title | escape }}
      </a>
    </h3>
    {%- if site.show_excerpts -%}
      {{ post.excerpt }}
    {%- endif -%}
  </li>
{%- endfor -%}
</ul>
