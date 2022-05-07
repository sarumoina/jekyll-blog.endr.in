---
title: 'Archive'
layout: post
---

<ul style="font-size:smaller">
    {% for post in site.posts %}
      <li class='mt-4'>
        <a class="is-capitalized" href="{{ post.url }}">{{ post.title }} </a> in {{ post.tags }}
      </li>
    {% endfor %}
</ul>

