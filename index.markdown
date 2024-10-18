---
layout: home
title: "Home"
---
Sometimes I'll write something and put it up here. You can also find me at [Github](https://github.com/Ffyud) or [LinkedIn](https://www.linkedin.com/in/ddyff/).

 [Teerkost](https://teerkost.nl), 
 [Stadjers-quiz](https://stadjers-quiz.nl),
 [Pluggar](https://ffyud.github.io/pluggar/)

<ul>
      {% for post in site.posts %}
        {% unless post.hidden %}
      <li>
            <a href="{{ post.url }}">{{ post.title }}</a>
      </li>
        {% endunless %}
      {% endfor %}
</ul>
