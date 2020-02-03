---
layout: home
title: Home
---

## [Blog](/blog/)

<ul>
{%- if site.posts.size > 2 -%}
  {%- for i in (0..1) -%}
  <li>{{ site.posts[i].date | date: "%Y-%m-%d" }} <a href="{{ site.posts[i].url | relative_url }}">{{ site.posts[i].title | escape }}</a></li>
  {%- endfor -%}
{%- endif -%}
</ul>

## [Wiki](/wiki.html)

## [Bookmarks](/bookmarks/)

## [About](/about/)
