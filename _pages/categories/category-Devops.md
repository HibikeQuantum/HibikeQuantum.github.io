---
title: "Devops"
layout: archive
permalink: categories/Devops
author_profile: true
sidebar_main: true
---

<!-- 왼쪽 navi_list_main 을 통해 categories/Devops 중간카테 고리로 왔을떄 보게되는 페이지 -->
<!-- use like this site.categories['white spaced title'] -->
<!-- 여기를 수정해서 카테고리의 값을 가져와 이터레이트 -->

{% assign posts = site.categories.Devops %}
{% for post in posts %} 
    {% include archive-single.html type=page.entries_layout %} 
{% endfor %}
{% if posts == "0" %}
    <span>0</span>
{% else %}
    <span>Need to be develope!!!</span>
{% endif %}