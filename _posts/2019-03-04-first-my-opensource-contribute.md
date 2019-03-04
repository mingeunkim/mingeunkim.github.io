---
layout: post
title: '사회초년생 개발자가 오픈소스에 기여하기까지'
description: '00년생 개발자가 올해 고등학교 졸업 후 오픈소스에 기여하기 까지'
date: 2019-03-04
author: 김민근
tags: [Laravel, opensource, ModernPUG, PHP, Slack, Release]
---

# 사회초년생 개발자가 오픈소스에 기여하기까지

안녕하세요. <br>해당 포스팅 제목과 같이 저는 빠르게 사회에 뛰어들은 개발자 김민근입니다.


저는 올해 특성화고 졸업을 무사히 마치고 바로 회사에 취업을 했습니다.

회사 퇴근 후 특별한 날이 아니면 자는 시간을 제외하곤 온종일 개발을 합니다.

저는 혼자 프로젝트 개발하는것을 좋아합니다.
회사일 외 개인적으로 프로젝트를 하고 싶어 혼자 프로젝트를 진행해봤지만
제 능력의 한계로 쳇바퀴돌듯 매번 그 기술을 재활용 하는 느낌이였습니다.
<br>
그래서 `오픈소스` 에 한번 기여를 해보자! 라고 생각을 했습니다.
<br>
.
<br>
.
<br>
.
<br>
.
<br>
그렇게 해서 시작된 오픈소스 여정


## 나에게 맞는 오픈소스를 찾다

우선 제일 자신 있는 `Laravel` 로 깃허브에 검색을 해가며 `help wanted` 라벨이 달린 저장소를 찾고 있었습니다.

찾는 도중 `Laravel Nova Localizations` 저장소를 발견 했고 지원하는 언어는 스무가지 넘게 있었습니다. 해당 저장소에서 제공하는 언어(Localization)는 사람들이 직접 번역을 해 Pull Request 날리고 있었습니다.
<br>
번역에 자신은 없지만 번역기의 힘을 빌려 기여를 해보기로 했습니다.

해당 저장소를 `fork` 한뒤 `en.json` 을 copy & paste `ko.json` 
`json` key / value 형식으로 저는 value 만 아래 사진처럼 번역을 진행 했습니다.

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/nova-source.png">

두번째로 `Pull Request` 를 날렸습니다. 과연 날린 PR이 닫힐까, 합쳐질까 기대하고 있는 사이
PR 날린 후 이틀 뒤 합쳐졌다고 이메일이 도착했습니다 !

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/nova-merge.png">


## 참여해도 될까요 ?

번역에 기여한뒤 저는 `ModernPUG` (PHP 개발자 모임) 슬랙에 참가 하였고, 모임 운영자님에게 해당 홈페이지 개발에 기여를 하고 싶다고 여쭤봤는데, 흔쾌히 수락을 하셨습니다.


## 그렇게 시작하게된 개발

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/modernpug-projects-panel.png">

운영자님께서 제게 하고 싶은것을 골라보라고 말씀을 해주셔서, 저는 홈페이지 개발에 할일 중 `PHP 관련 릴리즈 소식 업데이트` 를 찝었습니다.
<br>
구현 해야할 기능들은 3가지가 있습니다.
1. PHP 관련 릴리즈 크롤링
2. 릴리즈 소식 뷰
3. Slack 알림 발송

> 개발 관련된 내용은 따로 포스팅 하도록 하겠습니다.


## 개발 하면서 

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/modernpug-code-review.png">

개발을 하면서 많은 코드리뷰를 통해 제 코드가 많이 다듬어졌습니다.
<br>
코드리뷰에 대해서는 [여기](http://blog.logi-spot.com/%EC%BD%94%EB%93%9C%EB%A6%AC%EB%B7%B0%EC%9D%98-%EC%A7%84%EC%A7%9C-%EB%AA%A9%EC%A0%81%EC%9D%80-%EB%94%B0%EB%A1%9C%EC%9E%88%EB%8B%A4/)를 참고해주세요.