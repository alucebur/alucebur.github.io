---
layout: default
title: ""
description: "Alucebur's personal website"
---
List of projects:
{% for repository in site.github.public_repositories %}
  * [{{ repository.name }}]({{ site.url }}/{{ repository.name }}) - In Github: [{{ repository.name }}]({{ repository.html_url }})
{% endfor %}

<hr/>

{% for post in site.posts %}
  <p class='post-title'><a href='{{ post.url }}'>{{ post.title }}</a></p>
  <p class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</p>
  {{ post.excerpt }}
{% endfor %}
