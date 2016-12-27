---
layout: default
---

## Welcome to my blog

Hi there, I'm [Oliver Grieb](https://github.com/ogrieb) and I am starting this blog less for representational purposes - but as a somewhat structured way to start collecting some of my thoughts. 

The general idea will be to note down an interesting thought in a blog post so as to subsequently streamline those smaller pieces into certain topic related articles.

## Posts

{% for post in site.posts %}
  *{{ post.date | date_to_string }} » <a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a> blub
{% endfor %}
