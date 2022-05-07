---
title: 'Archive'
layout: post
---

<ul style="font-size:smaller">
    {% assign sorted_posts = site.posts | sort:"title" | capitalize %}
    {% for post in sorted_posts %}
      <li class='mt-4'>
        <a class="is-capitalized" href="{{ post.url }}">{{ post.title }} </a> {% if post.tag %} in <a href="/tags#{{post.tag}}">{{ post.tag }}</a> {% endif %}
      </li>
    {% endfor %}
</ul>

