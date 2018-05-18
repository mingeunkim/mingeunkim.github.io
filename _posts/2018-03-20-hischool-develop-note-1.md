---
layout: post
title: '하이스쿨 스토어 개발일기 1'
description: '전국고등학교 매점을 이어주는 사이트 개발일기'
date: 2018-03-20
author: 김민근
cover: 'https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/hischool-develop-note/note-1-logo.png'
tags: [Laravel, hischool, 하이스쿨스토어]
---

> 하이스쿨 스토어 개발일지 1

### 링크 
[http://hischools.kr](http://hischools.kr)

### 상세 날짜
- 개발 시작일 : Dec 21, 2017
- 라라벨 개발 시작일 : Feb 17, 2018
- 개발 일지 1 : Mar 20, 2018

### 상세 기능
- 로그인
- 회원가입
- 관리자 모드(dashboard) 추가중
- 매점 관리(매점주인 관리페이지) - 물품 목록, 물품 추가, 학생 관리, 결제 관리 (추가중), 설정(추가중)
- 매점 이용(학생 페이지) - 학교별 인증코드, 판매목록, 장바구니(추가중), 바로구매, 구매확정
- 번호표 시스템(24시간 기준과 학교별 기준으로 1부터 카운트)
- 구매내역


### 사용할 기술
- Docker
- Redis


### Request 경로 조회
1. 하이스쿨 스토어에서는 경로에 따라 네비게이션 바(navigation bar)가 달라지게 하고 있습니다.
기존 PHP 에서는 PHP_SELF 라는 글로벌 변수를 이용하여 해결을 하였는데 라라벨에서는 ```is``` 메소드를 제공해준다.

> resources/views/layouts/master.blade.php

```php
@if(request()->is('test1', 'test2'))
    // 네비게이션바 1
@else
    // 네비게이션바 2
@endif
```


### 번호표 시스템
1. sells 테이블에 이용자 학교코드인 행에 생성날짜와 현재 날짜와 같으면 생성날짜를 기준으로 내림차순 정렬한다.
2. 정렬된 행중 첫번째 행을 가져온다.
3. 만약 행이 없으면 기본 번호표는 1부터 시작한다.
4. 행이 있으면 그 행 번호표에 +1 을 부여한다.


```php
$checkSell = Sell::where('school_code', Auth::User()->school_code)
    ->where('created_at', 'like', $timeCheck->format('Y-m-d') . '%')
    ->orderBy('created_at','desc')
    ->first();

(!$checkSell) ? $plusNumber = 1 : $plusNumber = $checkSell->ticket_number + 1;
```


### 사용자 레벨관리
인증된 사용자의 레벨을 관리하는거에 대해서는 아래 사항이 있다.
1. 매점 주인 판별 및 관리자 판별여부
관리자 판별 여부도 아래 미들웨어와 동일하다.


> App\Http\Middleware\OwnerCheck.php


```php
if (Auth::User()->level < 1) {
    return redirect('access');
}
```