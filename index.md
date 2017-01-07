---
layout: default
---

## Welcome to my blog

Hi there, I'm [Oliver Grieb](https://github.com/ogrieb) and I'm starting this blog less for representational purposes, but as a somewhat structured way to start collecting some of my thoughts.

The general idea will be to note down an interesting thought in a blog post. Then, it will most likely get refined and extended a few times. Eventually, I might streamline some of those smaller pieces into a topic related article.

## Posts

{% for post in site.posts %}

* {{ post.date | date_to_string }} » [ {{ post.title }} ]( {{ post.url }} )

  {{ post.excerpt }}

{% endfor %}
