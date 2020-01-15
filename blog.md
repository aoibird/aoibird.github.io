---
layout: page
title: "Blog"
permalink: /blog/
---

<div class="blog">

  {%- if site.posts.size > 0 -%}
  <h2 class="post-list-heading">{{ page.list_title | default: "Posts" }}</h2>
  <ul class="post-list">
    {%- for post in site.posts -%}
    <li>
    {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
    <span class="post-meta">{{ post.date | date: date_format }}</span>
    <h3>
      <a class="post-link" href="{{ post.url | relative_url }}">
      {{ post.title | escape }}
     </a>
    </h3>
      {%- if site.show_excerpts -%}
      {{ post.description }}
      {%- endif -%}
    </li>
    {%- endfor -%}
  </ul>
  {%- endif -%}


  <h2>Categories</h2>
  <ul>
  {% for category in site.categories %}
  {%- assign date_format = site.minima.date_format | default: "%Y-%m-%d %A" -%}
    <li>{{ category[0] }}
    <ul>
    {% for post in category[1] %}
    <li><a href="{{ post.url }}">{{ post.title }}</a> {{ post.date | date: date_format }}</li>
    {% endfor %}
    </ul>
    </li>
  {% endfor %}
  </ul>


  <h2>Tags</h2>
  <ul>
  {% for tag in site.tags %}
  {%- assign date_format = site.minima.date_format | default: "%Y-%m-%d %A" -%}
    <li>{{ tag[0] }}
    <ul>
    {% for post in tag[1] %}
    <li><a href="{{ post.url }}">{{ post.title }}</a> {{ post.date | date: date_format }}</li>
    {% endfor %}
    </ul>
    </li>
  {% endfor %}
  </ul>
</div>
