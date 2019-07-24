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
  <p class="post-meta">
    <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%b %-d, %Y" }}</time>
    {% if post.edited %}
    (Updated: <time datetime="{{ post.edited | date_to_xmlschema }}">{{ post.edited | date: "%b %-d, %Y" }}</time>)
    {% endif %}
  </p>
  {{ post.excerpt }}
{% endfor %}
