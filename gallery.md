---
layout: page
title: aRt gallery
---

Lorem ipsum dolor sit amet, consectetur adipiscing elit. In in lorem et nisi dictum dictum vel sed mauris. Praesent efficitur pulvinar interdum. Integer ac egestas lectus. Suspendisse placerat auctor risus ac imperdiet. Etiam semper lorem ut elit mattis, sed molestie orci sagittis. Fusce urna orci, ornare eget nisl sed, ultricies commodo dui. Nunc ligula ex, aliquet et luctus vel, aliquet sit amet magna. Inspired by this [blog](http://accidental-art.tumblr.com/)

<ul class="gallery">
  {% for image in site.photo_gallery reversed %}
    <h3 class="image-title">{{ image.title }} - {{ image.date | date_to_string }} </h3>
      <img src="{{ image.image_path }}" alt="{{ image.title }}"/>
      {{ image.content }}
  {% endfor %}
</ul>