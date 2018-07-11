---
layout: default
permalink: "blog/index"
title: "Blog"
---

Distributed Systems (Some notes, mostly for my own reference):
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
