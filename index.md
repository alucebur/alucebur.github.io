---
layout: default
title: ""
description: "Alucebur's personal website"
---

## Welcome to my GitHub Pages

Warning: Under Heavy Work In Progress.

List of posts:
{% for post in site.posts %}
  * [{{ post.title }}]({{ post.url }})
{% endfor %}

List of projects:
{% for repository in site.github.public_repositories %}
  * [{{ repository.name }}]({{ site.url }}/{{ repository.name }}) - In Github: [{{ repository.name }}]({{ repository.html_url }})
{% endfor %}

{% for post in site.posts %}
  <p class='post-title'><a href='{{ post.url }}'>{{ post.title }}]<a></p>
  {{ post.excerpt }}
{% endfor %}
