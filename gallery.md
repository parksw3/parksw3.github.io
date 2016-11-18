---
layout: page
title: aRt gallery
---

I enjoy making figures using [R](https://www.r-project.org/), especially with `ggplot2` package. This page contains some of the images that I found interesting. Some of these will serve as mini blog posts or as extensions to existing blog posts. Most of the figures are derived from different projects I'm working on. I might post [accidental aRt](http://accidental-art.tumblr.com/) as well from time to time.

<ul class="gallery">
  {% for image in site.photo_gallery reversed %}
    <h3 class="image-title">{{ image.title }} - {{ image.date | date_to_string }} </h3>
      <img src="{{ image.image_path }}" alt="{{ image.title }}"/>
      {{ image.content }}
  {% endfor %}
</ul>