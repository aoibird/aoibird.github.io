---
layout: page
title: Categories & Tags
permalink: /categories-tags/
---

<h2>Categories<h2>
{% for category in site.categories %}
{%- assign date_format = site.minima.date_format | default: "%Y-%m-%d %A" -%}
  <h3>{{ category[0] }}</h3>
  <ul>
    {% for post in category[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a> {{ post.date | date: date_format }}</li>
    {% endfor %}
  </ul>
{% endfor %}

<h2>Tags<h2>
{% for tag in site.tags %}
{%- assign date_format = site.minima.date_format | default: "%Y-%m-%d %A" -%}
  <h3>{{ tag[0] }}</h3>
  <ul>
    {% for post in tag[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a> {{ post.date | date: date_format }}</li>
    {% endfor %}
  </ul>
{% endfor %}
