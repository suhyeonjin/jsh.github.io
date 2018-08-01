---
layout: page
title: About-Me
---
{% assign total_words = 0 %}
{% assign total_readtime = 0 %}
{% assign featuredcount = 0 %}
{% assign statuscount = 0 %}

{% for post in site.posts %}
    {% assign post_words = post.content | strip_html | number_of_words %}
    {% assign readtime = post_words | append: '.0' | divided_by:200 %}
    {% assign total_words = total_words | plus: post_words %}
    {% assign total_readtime = total_readtime | plus: readtime %}
    {% if post.featured %}
    {% assign featuredcount = featuredcount | plus: 1 %}
    {% endif %}
{% endfor %}

## Profile
<!--
<figure>
  <img src="{{ site.url }}/images/profile.jpg">
  <figcaption>At Sanfrancisco RSA 2017</figcaption>
</figure>
-->

> 업데이트 & 수정 중

##### [Introduce]
  - 1993.05.04
  - SU HYEON, JIN

##### [Operation System]
  - Windows
  - Linux
  - macOS

##### [Language]
  - Major : Python
  - Minor : C/C++, Java, PHP, javascript(ajax, jqeury...)
  - ETC : C#, Android, Shell Script, Markup

##### [Database]
  - Mysql, MongoDB(NoSQL)
  - Oracle
    - Procedure, PL/SQL

##### [Web Server]
  - Python(Flask + Gevent)
  - Apache

##### [Framework]
  - Flask, Jinja, Django
  - Elastic search, Logstash, Kibana

##### [Server Framework]
  - Docker

##### [Eligibility]
  - 정보처리 산업기사
  - Digital Forensic Expert  2rd

##### [Career]
  - (2014.07.20 ~ 2016.07.21) 공군본부 중앙전산소 침해사고 대응팀 - CERT
  - (2016.07.01 ~ 2017.03.30) 차세대 보안리더 양성 프로그램 Best of the Best 5th
    - Digital Forensic Track


  - (2016.11.28 ~ 2017.02.28) LG 전자 서초 R&D, SW Platform SDL & Security part
    - IoT SDL Secure coding Guide


  - (2017.06.26 ~ 2017.08.25) 한국 전자통신 연구소 (ETRI), 지능보안연구 그룹
    - Moving Target Defense Project

##### [Presentation / Project]
  - Best of the Best 5th Project
    - IoT Hacking & Forensic with 0-day


  - LG 전자 서초 R&D 취약점 시연회
  - 독일 하이델베르크 TROOPERS17 NGI Session Speaker
    - IoT Hacking & Forensic with 0-day


  - 동계 정보보호 학술 대회 논문
    - 기계학습을 이용한 행위 기반의 악성코드 판별 모델


  - 일본 도쿄 AVTOKYO Conference Speaker
    - IoT Hacking & Forensic with 0-day


  - 고려대학교 정보보호 R&D Challenge 참가
    - CAN Network Traffic (차량 네트워크 이상 징후 탐지 시스템 개발)

##### [Awards]
  - 2016 KR/FE 훈련. 사이버 DDoS 악성코드 탐지 및 방어
    - 참모총장 표창 및 포상 휴가 수상


  - (2016.12.13) LG 전자 보안 취약점 탐지 감사장
    - LG전자 제품 취약점 5건 제보
    - 부사장상 민경오 수여


  - (2017.03.30) 차세대 보안리더 Best 10 선정
    - 미래창조과학부 장관상 수여


  - (2017.04.20) 2017 Codegate 해킹 시연영상 특별상
    - Codegate 수여


  - (2017.11.02) 제 4회 SW 개발보안 경진대회 우수상
    - 한국인터넷진흥원장상 수여


  - (2017.12.12) 제 5회 디지털 범인을 찾아라 대상
    - 검창총장상 수여


  - (2017.11.16) 2017 WhiteHat CTF 본선
    - 본선 8위


  - 그 외 다수 CTF 참가

