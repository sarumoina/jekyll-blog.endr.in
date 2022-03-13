---
title: 'Archive'
layout: post
---

<span class="title is-size-4">Posts</span>
<ul>
    {% for post in site.posts %}
      <li>
        <a class="is-capitalized" href="{{ post.url }}">{{ post.title }}</a>
      </li>
    {% endfor %}
</ul>