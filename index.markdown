---
layout: home
title: "Home"
---
Sometimes I'll write something and put it up here. You can also find me at [Github](https://github.com/Ffyud) or [LinkedIn](https://www.linkedin.com/in/ddyff/).

 [Teerkost](https://teerkost.nl), 
 [Stadjers-quiz](https://stadjers-quiz.nl)

<ul>
      {% for post in site.posts %}
      <li>
            <a href="{{ post.url }}">{{ post.title }}</a>
      </li>
      {% endfor %}
</ul>
