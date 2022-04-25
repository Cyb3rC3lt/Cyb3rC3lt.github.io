---
layout: bloglayout
title: Blog
---
<h1>Latest Posts</h1>

<ul>
  {% for post in site.posts %}
<p><ul>{{ page.date | date_to_string }}</ul></p>

    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>

<br>
<a href="/index">Back</a>
