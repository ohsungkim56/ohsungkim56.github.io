---
title: "front contents"
layout: archive
permalink: categories/front
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.front %}
{% for post in posts reversed %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}