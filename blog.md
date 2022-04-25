---
layout: bloglayout
title: Blog
---
<h1>Latest Posts</h1>

<u>
  {% for post in site.posts %}
<p><ul>{{ page.date | date_to_string }}</u></p>

    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>

<br>
<a href="/index">Back</a>
