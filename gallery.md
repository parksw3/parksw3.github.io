---
layout: page
title: aRt gallery
---

Under development... Inspired by this [blog](http://accidental-art.tumblr.com/)

<ul class="gallery">
  {% for image in site.photo_gallery reversed %}
    <h3 class="image-title">{{ image.title }} - {{ image.date | date_to_string }} </h3>
      <img src="{{ image.image_path }}" alt="{{ image.title }}"/>
  {% endfor %}
</ul>