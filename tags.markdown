---
title: 'Tags'
layout: post
---

{% assign sorted_tags = site.tags | sort %}

{% for tag in site.tags %}
    <h3>{{tag[0]}}</h3>
    {% assign sorted_posts = tag[1] | sort:"title" %}
    <ul>
        {% for post in sorted_posts %}
        <li><a href="{{ post.url }}">{{ post.title }}</a></li>
        {% endfor %}
    </ul>
{% endfor %}