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
  * [{{ repository.name }}]({{ repository.html_url }})
{% endfor %}
