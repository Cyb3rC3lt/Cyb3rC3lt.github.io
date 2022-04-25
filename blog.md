---
layout: bloglayout
title: Blog
---
<h1>Latest Posts</h1>

<ul>
  {% for post in site.posts %}
    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      <p><u>{{ post.date | date_to_string }}</u></p>
{{ post.excerpt }}
    </li>
  {% endfor %}
</ul>

<br>
<a href="/index">Back</a>
