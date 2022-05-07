---
title: 'Archive'
layout: post
---

<ul style="font-size:smaller">
    {% for post in site.posts %}
      <li class='mt-4'>
        <a class="is-capitalized" href="{{ post.url }}">{{ post.title }} </a> <ul>
  {% for tags in page.tags %}
    <li>{{ tags }}</li>
  {% endfor %}
</ul>
      </li>
    {% endfor %}
</ul>

