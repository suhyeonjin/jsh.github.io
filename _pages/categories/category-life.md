---
title: "Post about Life"
layout: archive
permalink: /categories/life
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.life | sort:"date" %}

{% for post in posts %}
  {% include archive-single.html type=page.entries_layout %}
{% endfor %}

