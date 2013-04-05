---
layout: page
title: Hwijae Lee's Blog <a href='http://feeds.feedburner.com/HwijaeLeesBlog'><img src='/images/feed32.png' alt='' /></a>
---
{% include JB/setup %}

프로그래머, 아빠 - 사진, 기타, 글쓰기

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date: "%Y-%m-%d" }}</span> - <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
