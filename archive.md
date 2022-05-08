---
title: 'Archive'
layout: post
---

<ul>
    {% assign sorted_posts = site.posts | sort:"title"  %}
    {% for post in sorted_posts %}
      <li class='mt-4'>
        <a class="is-capitalized" href="{{ post.url }}">{{ post.title }} </a> {% if post.tag %} in [<a href="/tags#{{post.tag | slugify}}">{{ post.tag }}</a>] {% endif %}
      </li>
    {% endfor %}
</ul>

