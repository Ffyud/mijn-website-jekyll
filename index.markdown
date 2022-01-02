---
layout: home
title: "Home"
---
Sometimes I'll write something and put it up here. You can also find me at [Github](https://github.com/Ffyud).

Check out my news collecting website for the city of Groningen, called [Brakdag](https://brakdag.nl). Or have a go at this little quiz about the same city named [Stadjers-Quiz](https://stadjers-quiz.nl). Also working on a [Jekyll](https://jekyllrb.com/) theme called [Paloma Jekyll](https://github.com/Ffyud/paloma-jekyll) (actually used on this website).

<ul>
      {% for post in site.posts %}
      <li>
            <a href="{{ post.url }}">{{ post.title }}</a>
      </li>
      {% endfor %}
</ul>
