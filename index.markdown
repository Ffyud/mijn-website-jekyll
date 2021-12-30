---
layout: home
title: "Home"
---
# David
👨‍💻 Sometimes I'll write something and put it up here. You can also find me at [Github](https://github.com/Ffyud).

📰 Check out my news collecting website for the city of Groningen, called [Brakdag](https://brakdag.nl). Made with React, Python and Docker. 

🏆 Or have a go at this little quiz about the same city named [Stadjers-Quiz](https://stadjers-quiz.nl). Made with React and hosted on Github as a static website.

🎨 Also working on a [Jekyll](https://jekyllrb.com/) theme called [Paloma Jekyll](https://github.com/Ffyud/paloma-jekyll) (actually used on this website).

# Blog

<ul>
      {% for post in site.posts %}
      <li>
            <a href="{{ post.url }}">{{ post.title }}</a>
      </li>
      {% endfor %}
</ul>
