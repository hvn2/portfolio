---
layout: default
title: Blogs
---

## Blogs

Check out my blogs and tutorials on image processing, machine leanring and deep learning (Some of them are in Vietnamese):

<ul class="post-list">
  {% for post in site.posts %}
    <li>
      <h3><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
      <p><small>{{ post.date | date: "%B %d, %Y" }}</small></p>
      <p>{{ post.excerpt }}</p>
    </li>
  {% endfor %}
</ul>