---
title: "Today I Learned 모음"
layout: archive
permalink: categories/TIL
author_profile: true
sidebar_main: true
---
{% assign posts = site.categories.TIL %}
    {% for post in posts %} 
        {% include archive-single.html type=page.entries_layout %} 
    {% endfor %}


<!-- Open graph 활용한 포맷
[![Readme Card](https://github-readme-stats.vercel.app/api/pin/?username=ansohxxn&repo=coding-test)](https://github.com/ansohxxn/coding-test) 
-->

<!-- {% assign posts = site.categories.BOJ %}
{% for post in posts %} 
    {% include archive-single2.html type=page.entries_layout %} 
{% endfor %} -->


<!-- 왼쪽 navi_list_main 을 통해 categories/Devops 중간카테 고리로 왔을떄 보게되는 페이지 -->
<!-- use like this site.categories['white spaced title'] -->
<!-- 필요하면 카드형 뉴스나 외부 링크, 짤막한 소개를 여기 써도 된다. 여기를 수정해서 카테고리의 값을 가져와 이터레이트 -->
