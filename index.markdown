---
title: Home
layout: default
---
[**Study**](/study.html) is written for easy grasping of the sociological content. 

[**Cheatsheet**](/cheatsheet) is written in order to bring all the related information of building a VPS in to one place. It has helped me as reference from time to time and I hope that some other may also benefit from it. For any mistakes/improvements, send a mail contact@endr.in

[**Check Archive**](/archive) for all posts. 

{% for post in site.posts %}
{{ post.title }} {{post.url}} <br />
{% endfor %}