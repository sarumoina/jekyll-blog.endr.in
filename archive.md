---
title: 'Archive'
layout: post
---

<ul style="font-size:smaller">
    {% assign sorted_posts = site.posts | sort %}
    {% for post in sorted_posts %}
      <li class='mt-4'>
        <a class="is-capitalized" href="{{ post.url }}">{{ post.title }} </a> {% if post.tag %} in {{ post.tags }} {% endif %}
      </li>
    {% endfor %}
</ul>

