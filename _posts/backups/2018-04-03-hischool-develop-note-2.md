---
layout: post
title: '하이스쿨 스토어 개발일기 2'
description: '전국고등학교 매점을 이어주는 사이트 개발일기'
date: 2018-04-03
author: 김민근
cover: 'https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/hischool-develop-note/note-1-logo.png'
tags: [Laravel, hischool, 하이스쿨스토어]
---

> 하이스쿨 스토어 개발일기 2탄

### 링크 
[https://hischools.kr](https://hischools.kr)

### 상세 날짜
- 개발 시작일 : Dec 21, 2017
- 라라벨 개발 시작일 : Feb 17, 2018
- 개발 일지 1 : Mar 20, 2018
- 개발 일지 2 : Apr 03, 2018

### 상세 기능
- 로그인
- 회원가입
- 관리자 모드 - 실시간 이용자 확인
- 매점 관리(매점주인 관리페이지) - 물품 목록, 물품 추가, 학생 관리, 결제 관리, 설정
- 매점 이용(학생 페이지) - 학교별 인증, 판매목록, 장바구니, 바로구매, 구매확정
- 번호표 시스템
- 구매내역
- 설정 - 비밀번호 변경, 기타개인정보 변경


새로운 디자이너 최형주(dexloper)님의 합류로 전반적인 사이트 디자인이 변경되었습니다.

### 새로워진 로고
<img src="https://hischools.kr/images/og/symbol_version.png" width="500">

### 그외 ..
- 사이트의 폰트를 나눔스퀘어라운드로 교체하였다.
- Lets Encrypt SSL 을 적용 하였다.
- 디자이너의 합류로 할일을 공유해야하기 때문에 meistertask 라는 협업툴을 도입하였다.


### 구매 내역 시스템

현재 구매내역 시스템은 ```items``` 테이블과 ```sells``` 테이블을 조인하여 정보를 보여준다. 보여줌에 있어 orderby(정렬) 방식을 결정해야한다.
```sells``` 테이블의 status 를 기준으로 ```CASE WHEN THEN``` 절을 이용하여 조건에 따라 값을 지정해주고, ```sells```의 생성일을 기준으로 내림차순한다.

```php
$sellList = Item::where('id', Auth::User()->userid)->leftJoin('sells', function($join) {
            $join->on('sells.item_id', 'items.item_id');
        })
        ->orderByRaw("CASE sells.status WHEN 'wait' THEN 1 WHEN 'success' THEN 2 WHEN 'noshow' THEN 3 END")->orderBy('sells.created_at', 'desc')
        ->get();
```


### 학생 정보 출력
기본적으로 매점 주인이 학생 정보를 볼때 modal 형태로 띄어서 학생의 정보를 보여준다. 하지만 학생이 많아질수록 코드상 modal 의 수는 많아질것이다.

1. 학생 정보 버튼을 누르면 ```student_export()``` 함수에 학생의 아이디를 넘긴다.
2. ```student_export()``` 함수에서는 아이디를 받고 ```GET``` 방식으로 학생의 아이디를 넘긴다.
3. 서버에서 받고 학생의 아이디를 조회한뒤 json방식으로 정보를 뿌린다.
4. 받은 정보를 ```modal_body()``` 함수를 통해 보여준다.

```php
$user = User::where('id', $id)
    ->where('school_code', Auth::User()->school_code)
    ->first();
        return response()->json([
            'id' => $user->id,
            'name' => $user->name,
            'phone' => $user->phone,
            'buy_count' => $user->buy_count,
            'law_count' => $user->law_count,
            'register' => $user->created_at
        ]);
```

```js
$(document).ready(function () {
    $(".owner-status .view").click(function (event) {
        student_export($(this).attr('studentid'));
    });
});

function student_export(id) {
    $.ajax({
		type: 'GET',
		url: '/owner/student/'+id,
        success: function (data) {
        modal_body(data);
        }
	})
}

function modal_body(data) {
    $("#studentModal").modal(data);
    $(".modal-title").text(data.name + ' (' + data.id + ')');
    $(".modal-contents").html('이름 : ' + data.name + 
    '<br>번호 : ' + data.phone + '<br>구매 횟수 : ' +
    data.buy_count + '<br>노쇼 횟수 : ' + data.law_count);
}
```