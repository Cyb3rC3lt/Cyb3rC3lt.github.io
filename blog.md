---
layout: bloglayout
title: Blog
---
<h1>Latest Posts</h1>
  {% for post in site.posts %}
<p><u>{{ page.date | date_to_string }}</u></p>

    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      {{ post.excerpt }}
    </li>
  {% endfor %}

<br>
<a href="/index">Back</a>
