---
layout: page
title: Home
---

## [Blog](/blog/)

<ul>
{%- if site.posts.size > 2 -%}
  {%- for i in (0..1) -%}
  <li>
    <span class="post-meta">{{ site.posts[i].date | date: "%b %-d, %Y" }}</span>
    <a href="{{ site.posts[i].url | relative_url }}">{{ site.posts[i].title | escape }}</a>
  </li>
  {%- endfor -%}
{%- endif -%}
</ul>

## [Bookmarks](/bookmarks/)

{% include page-excerpt.html attr="name" value="bookmarks.md" %}
