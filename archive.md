---
title: 'Archive'
layout: post
---

<ul>
    {% for post in site.posts %}
      <li>
        <a class="is-capitalized" href="{{ post.url }}">{{ post.title }}</a>
      </li>
    {% endfor %}
</ul>