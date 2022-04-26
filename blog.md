---
layout: bloglayout
title: Blog
---
<div class="blogheader"><h1>Latest Posts</h1><a href="/">Back</a>
</div> 

<ul>
  {% for post in site.posts %}
    <li>
        <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
<div class="excerpt">      
  {{ post.excerpt }}
 </div>
    </li>
  {% endfor %}
</ul>

<br>
<a href="/">Back</a>
