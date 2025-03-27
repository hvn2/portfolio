---
layout: default
title: Blog
permalink: /blog/
---
## Blog  
Check out my latest posts below:  
{% for post in site.posts %}
  - [{{ post.title }}]({{ post.url }}) - {{ post.date | date: "%B %d, %Y" }}
{% endfor %}