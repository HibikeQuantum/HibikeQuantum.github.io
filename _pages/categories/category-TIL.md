---
title: "하루를 통해 배운것과 내 마음을 정리하기 위한 공간"
layout: archive
permalink: categories/TIL
author_profile: true
sidebar_main: true
---

<!-- use like this site.categories['white spaced title'] -->
{% assign posts = site.categories.TIL %}
    {% for post in posts %} 
        {% include archive-single.html type=page.entries_layout %} 
    {% endfor %}