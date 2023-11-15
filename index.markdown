---
layout: home
title: "Home"
---
Sometimes I'll write something and put it up here. You can also find me at [Github](https://github.com/Ffyud) or [LinkedIn](https://www.linkedin.com/in/ddyff/).

Check out my grocery discount collector project called [Teerkost](https://teerkost.nl). Or have a go at this little quiz I made about the same city named [Stadjers-Quiz](https://stadjers-quiz.nl). 

<ul>
      {% for post in site.posts %}
      <li>
            <a href="{{ post.url }}">{{ post.title }}</a>
      </li>
      {% endfor %}
</ul>
