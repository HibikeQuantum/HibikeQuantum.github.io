---
title: "Devlog"
layout: archive
permalink: categories/TIL
author_profile: true
sidebar_main: true
---

<!-- use like this site.categories['white spaced title'] -->
{% assign posts = site.categories.Devlog %}
    {% for post in posts %} 
        {% include archive-single-group.html type=page.entries_layout %} 
    {% endfor %}