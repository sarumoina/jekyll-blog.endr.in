---
title: 'Archive'
layout: post
---

<ul style="font-size:smaller">
    {% for post in site.posts %}
      <li class='mt-4'>
        <a class="is-capitalized" href="{{ post.url }}">{{ post.title }}</a>
      </li>
    {% endfor %}
</ul>

{% for tag in site.tags %}
  <h3>{{ tag[0] }}</h3>
  <ul>
    {% for post in tag[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}