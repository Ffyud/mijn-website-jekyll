---
layout: default
title: "Docs"
permalink: /docs/
---

<h1>Docs</h1>

<ul>
      {% for post in site.posts %}
      <li>
            <a href="{{ post.url }}">{{ post.title }}</a>
      </li>
      {% endfor %}
</ul>
