---
layout: page
title: aRt gallery
---

I enjoy making figures with [R](https://www.r-project.org/), especially using `ggplot2` package. This page contains images that I find interesting. Some of these figures may serve as mini blog posts on their own or as extensions to existing blog posts. Most of these are be derived from different projects I'm working on but may not have much meaning. This page was inspired by [accidental aRt](http://accidental-art.tumblr.com/).

<ul class="gallery">
  {% for image in site.photo_gallery reversed %}
    <h3 class="image-title">{{ image.title }} - {{ image.date | date_to_string }} </h3>
      <img src="{{ image.image_path }}" alt="{{ image.title }}"/>
      {{ image.content }}
  {% endfor %}
</ul>