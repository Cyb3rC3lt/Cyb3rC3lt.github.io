---
layout: default
title: Blog
---
<h1>Latest Posts</h1>
<a href="/index">Back</a>
<ul>
  {% for post in site.posts %}
    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>
