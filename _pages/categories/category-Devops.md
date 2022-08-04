---
title: "Devops Title"
layout: archive
permalink: categories/Devops
author_profile: true
sidebar_main: true
---

<!-- use like this site.categories['white spaced title'] -->
{% assign posts = site.categories.Devops %}
    {% for post in posts %} 
        {% include archive-single-group.html type=page.entries_layout %} 
    {% endfor %}