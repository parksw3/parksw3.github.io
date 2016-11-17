---
layout: page
title: aRt gallery
---

I enjoy making figures with [R](https://www.r-project.org/), especially using `ggplot2` package. Some of these figures will serve as an extension to blog posts, but most of them will be derived from different things I'm working on. This page was inspired by [accidental aRt](http://accidental-art.tumblr.com/).

<ul class="gallery">
  {% for image in site.photo_gallery reversed %}
    <h3 class="image-title">{{ image.title }} - {{ image.date | date_to_string }} </h3>
      <img src="{{ image.image_path }}" alt="{{ image.title }}"/>
      {{ image.content }}
  {% endfor %}
</ul>