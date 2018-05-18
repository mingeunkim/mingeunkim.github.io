---
layout: post
title: '하이스쿨 스토어 개발일기 3'
description: '전국고등학교 매점을 이어주는 사이트 개발일기'
date: 2018-04-23
author: 김민근
cover: 'https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/hischool-develop-note/note-1-logo.png'
tags: [Laravel, hischool, 하이스쿨스토어]
---

> 하이스쿨 스토어 개발일기 3탄

### 링크 
[https://hischools.kr](https://hischools.kr)

### 상세 날짜
- 개발 시작일 : Dec 21, 2017
- 라라벨 개발 시작일 : Feb 17, 2018
- 개발 일지 1 : Mar 20, 2018
- 개발 일지 2 : Apr 03, 2018
- 개발 일지 3 : Apr 23, 2018

### 상세 기능
- 로그인
- 회원가입
- 관리자 모드 - 실시간 이용자 확인
- 매점 관리(매점주인 관리페이지) - 물품 목록, 물품 추가, 학생 관리, 결제 관리, 설정
- 매점 이용(학생 페이지) - 학교별 인증, 판매목록, 장바구니, 바로구매, 구매확정
- 쿠폰 시스템(할인)
- AR EVENT (증강현실)
- 번호표 시스템
- 구매내역
- 설정 - 비밀번호 변경, 기타개인정보 변경


이렇게 이쁜 디자인은 NOW UI PRO 를 사용하였음.


## 추가된 기능들

### 안드로이드 어플리케이션 개발
<img src="https://github.com/getsolaris/getsolaris.github.io/blob/master/assets/images/post/hischool-develop-note/app.jpeg?raw=true" width="300">


### 증강현실(AR) 이벤트
증강현실 구현은 arjs 로 구현을 하였다. 이벤트 페이지 브라우저를 열어 카메라를 허용한뒤, 마커를 인식하면 프로모션 코드가 나타나는 방식이다.
<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/hischool-develop-note/ar.png" width="300">


### 장바구니 아이템 나누기
일반 학생들이 장바구니에 여러 물건을 담아 예약을 하면 ```sells``` 테이블에 티켓번호가 증가하고 그 티켓번호에 중첩되어 데이터들이 쌓인다.
예) 오감자, 새우깡을 예약했으면 두개의 아이템으로 분류되어 한 행씩 데이터가 쌓이게된다. 해당 티켓번호는 같이 예약했음으로 동일한 티켓번호이다.


매점 주인페이지에서는 같은 티켓번호를 가진 물품들을 한 행에 같이 출력을 할것이다. 같은 티켓번호를 여러행으로 출력하게되면 사용하기 불편할것이다.
예) 학생이 오감자, 새우깡, 만두를 예약했다. 티켓번호를 3번으로 가정하에 ```get()``` 을 사용하면 3개의 행이 출력될것이다.

당일 예약된 물품들만 출력하기 위에 아래 세번째 라인에 ```where``` 절을 이용하여 연도, 달, 날 이 같을 경우만 필터링한다.

```ticket_number``` (티켓번호) 를 묶어 데이터를 출력 해줘야함으로 ```groupBy``` 절을 사용한다.

```php
$buyLists = Sell::join('items', 'sells.item_id', '=', 'items.item_id')
    ->select('sells.*', 'items.item_title', 'items.item_price')
    ->where('sells.created_at', 'like', $timeCheck->format('Y-m-d') . '%')
    ->get();
$groupedBuyLists = $buyLists->groupBy('ticket_number');
```


view 부분에서는 ```$groupedBuyLists``` 를 foreach 문을 돌려 출력시킨다. ```pluck``` 는 주어진 키에 모든 값을 반환하는 메소드이다.

(( 이 부분은 stackoverflow 커뮤니티의 도움을 받음 ))

```php
$title = implode(', ', $sells->pluck('item_title')->all()) }}
```


### 쿠폰 시스템

쿠폰은 하이스쿨 스토어 자체 이벤트로 얻을 수 있다. 사용자들은 코드입력 탭에서 코드를 입력하면, 그에 맞는 코드가 있으면 쿠폰을 지급해준다.

쿠폰은 구매페이지에서 사용이 가능하다. 쿠폰은 사용 안했으면 1, 사용 했으면 0을 가진다. 사용한 쿠폰은 출력하지않는다.

```php
$coupons = Coupon::join('owns', 'coupons.coupon_id', '=', 'owns.coupon_id')
    ->where('owns.status', 1)
    ->where('owns.user_id', Auth::User()->userid)
    ->get();
```

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/hischool-develop-note/coupon.png" width="500">