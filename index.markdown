---
layout: home
title: "Home"
---
Sometimes I'll write something and put it up here. You can also find me at [Github](https://github.com/Ffyud) or [LinkedIn](https://www.linkedin.com/in/ddyff/).

Check out my news collecting website for the city of Groningen, called [Brakdag](https://brakdag.nl). Or have a go at this little quiz I made about the same city named [Stadjers-Quiz](https://stadjers-quiz.nl). 

Another project is [Teerkost](https://teerkost.nl) which collects current supermarket discounts and tries to present them in a fast and clear way.

<ul>
      {% for post in site.posts %}
      <li>
            <a href="{{ post.url }}">{{ post.title }}</a>
      </li>
      {% endfor %}
</ul>
