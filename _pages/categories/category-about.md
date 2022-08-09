---
title: "나에 대한 이야기"
layout: archive
permalink: categories/about
author_profile: true
sidebar_main: true
---
{% assign posts = site.categories.about %}
{% for post in posts %} 
    {% include archive-single.html type=page.entries_layout %} 
{% endfor %}