---
layout: page
title: aRt gallery
---

I enjoy making figures using [R](https://www.r-project.org/), especially with `ggplot2` package. This page contains images that I found interesting. Some of these will serve as mini blog posts or as extensions to existing blog posts. Most of the figures are be derived from different projects I'm working on, but they may not have much meaning. This page was inspired by [accidental aRt](http://accidental-art.tumblr.com/).

<ul class="gallery">
  {% for image in site.photo_gallery reversed %}
    <h3 class="image-title">{{ image.title }} - {{ image.date | date_to_string }} </h3>
      <img src="{{ image.image_path }}" alt="{{ image.title }}"/>
      {{ image.content }}
  {% endfor %}
</ul>