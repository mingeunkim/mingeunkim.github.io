---
layout: post
title: '하이스쿨 스토어 API 개발 1'
description: '전국고등학교 매점을 이어주는 사이트 개발일기'
date: 2018-05-04
author: 김민근
tags: [Laravel, hischool, 하이스쿨스토어, api]
---

> 하이스쿨 스토어 API 개발 1

### 링크 
[https://hischools.kr](https://hischools.kr)

### 개발 배경
카카오톡 및 페이스북 챗봇을 개발하여 학교 학생들에게 재고 현황을 손쉽게 알려줄 수 있다는 장점으로 API 서버를 개발 하게 되었습니다. 하이스쿨 스토어에서는 학생들이 손쉽게 개발(구현)할 수 있도록 API를 제공해줄뿐 챗봇 개발을 맡아 해주지는 않습니다.. 개발 가이드라인은 현재 PHP만 존재합니다. (다른 언어의 가이드라인은 학생들이 직접 자신의 코드를 공개할 수 있도록 할것입니다) 


1. ```token```은 개발할 학생이 신청할 수 있으며, 한 학교당 하나만 발급이 가능합니다.
2. API 요청시 ```request_code```(요청 수) 가 요청할때마다 증가합니다.


## 개발

개발 테스트 토큰은 1012312481247124 이다.

| method |            uri            |                              |
|:------:|:-------------------------:|------------------------------|
|   GET  |            v1.0           | 버전 소개                    |
|   GET  |       token/{token}       | 토큰 정보 (요청수, 기타정보) |
|   GET  | school/v1.0/items/{token} | 물품 정보                    |

### 토큰 정보

| token/{token} |          |
|:-------------:|:-----------:|
| request_count |    요청수   |
|     school    |    학교명   |
|  school_code  |   학교코드  |


### 물품 정보

| school/v1.0/items/{token} |            |
|:-------------------------:|:-------------:|
|         item_title        |    물품 명    |
|        item_comment       |   물품 소개   |
|         item_buyer        | 물품 구매자수 |
|        item_status        |   물품 상태   |
|         item_price        |   물품 가격   |
|         item_image        |  물품 이미지  |
|         created_at        |               |
|         updated_at        |               |

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/hischool-develop-note/api_1.png" width="500">

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/hischool-develop-note/api_2.png">


## 파싱

> PHP


```php
class JsonController extends Controller
{
    public function index() {
        $token = "1012312481247124"; // 발급받은 token
        $url = 'https://hischools.kr/api/school/v1.0/items/' . $token;
        $content = file_get_contents($url);
        $json = json_decode($content, true);
        
        // print_r($json);

        foreach($json['items'][0] as $item) {
            echo $item['item_title'];
        }
    }
}
```



