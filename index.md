---
layout: index
title: index
tagline:
---

{% include JB/setup %}

最近发布的文章

{% for post in site.posts limit:10 %}
{{ post.date | date_to_string }} » {{ post.title }}
{% endfor %}
