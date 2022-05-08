---
title: 'Tags'
layout: post
---

{% assign sorted_tags = site.tags | sort:tag[0] %}

{% for tag in site.tags %}

##### <ion-icon name="pricetag-outline"></ion-icon> {{tag[0] | capitalize }}

{% assign sorted_posts = tag[1] | sort:"title" %}
<ul>
    {% for post in sorted_posts %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>
{% endfor %}