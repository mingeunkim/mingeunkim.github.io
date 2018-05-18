---
layout: post
title: 'paperplanes.today'
description: 'RESTful 비슷하지만 아닌 종이비행기 개발일기'
date: 2017-12-21
author: 김민근
cover: 'https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/197ee8682a5442b0f3acad1ac7e06585871e84df/assets/images/post/paperplanes/logo.png'
tags: [PHP, slim, ajax, papercss]
---

> slim framework 를 이용하여 restful api 서버를 개발하다.

[사이트 바로가기 →](http://paperplanes.today)

[깃허브 바로가기 →](https://github.com/paperplanes-today)


### 소개
페이퍼플레인은 힘든 사람들을 위해 위로 혹은 힘이 되는 말을 전해주는 사이트이다. 웹 구성은 클라이언트와 서버 부분을 나누어 개발을 진행하였다. 클라이언트 부분은 깃허브 호스팅을 이용하여 구동 중이고, 서버 부분은 아마존 웹서비스를 이용 중이다. 페이스북이나 트위터 같은 SNS 사이트에서 이용되는 무한 스크롤링이라는 기술을 사용했다. (오픈소스)

### 사용한 기술
- slim framework (PHP)
- jQuery
- aws

프론트 프레임워크는 papercss 를 이용하였음.


### 슬림 프레임워크란 ?
슬림은 PHP 마이크로 프레임워크이다. 비슷한 프레임워크로는 라라벨의 루멘 이라는 프레임워크가 존재한다.

- [슬림 프레임워크](https://www.slimframework.com/)
- [루멘 프레임워크](https://lumen.laravel.com/)

슬림을 이용하면 쉽고 빠른 API 서버 개발이 가능하다. 

```php
<?php
use \Psr\Http\Message\ServerRequestInterface as Request;
use \Psr\Http\Message\ResponseInterface as Response;

require 'vendor/autoload.php';

$app = new \Slim\App;
$app->get('/hello/{name}', function (Request $request, Response $response, array $args) {
    $name = $args['name'];
    $response->getBody()->write("Hello, $name");

    return $response;
});
$app->run();
```

### 알게된것
1. HTTP 접근 제어 (cross origin resource sharing)
- 처음 전송되는 리소스의 도메인과 다른 도메인으로부터 리소스가 요청될 경우 해당 리소스는 cross-origin HTTP 요청에 의해 요청됨.
- 페이퍼플레인은 클라이언트 서버는 깃허브 호스팅을 이용하고 서버는 aws 를 이용하기 때문에 ip가 다를 수 밖에 없다. ip가 다르기 때문에 ajax 로 api서버에 접근을 해도 반응이 없었다. 검색을 해보니 cors 때문이라는것을 알게 되었다.
- [자세한 내용](https://docs.microsoft.com/ko-kr/aspnet/core/security/cors)
```php
$app -> add(function ($req, $res, $next) {
    $response = $next($req, $res);
    return $response
            -> withHeader('Access-Control-Allow-Origin', 'http://paperplanes.today')
            -> withHeader('Access-Control-Allow-Headers', 'X-Requested-With, Content-Type, Accept, Origin, Authorization')
            -> withHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
});
```
2. nl2br 함수
- 해당 함수는 문자열중 줄바꿈을 HTML 상에서 \<br\> 로 표시해준다.
- 해당 함수는 이용자들이 글을 작성 할 때 줄 바꿈을 이용하여 작성 시 그대로 출력을 하게 되면 줄 바꿈이 안되어 출력된다. 해당 함수를 사용하면 줄 바꿈 처리됨.

```php
$name = "김\n민\n근";

echo nl2br($name);
/*
output :
김
민
근
/*
```

### RESTful API
- 슬림 프레임워크 강좌를 보면서 restful api 라는 단어가 귀에 들어오게 되었다. [자세한 내용](http://meetup.toast.com/posts/92)
- 페이퍼플레인 개발 당시 restful 디자인가이드에 익숙하지 않아 URL 규칙을 따르지 못했다.


### 라우트 지정

|  <center>method</center> |  <center>comment</center> |
|:--------:|--------:|
| <center>POST (/paperplanes/token)</center> | <center>크로스 도메인 방지를 위해 이용자의 ip를 암호화 하여 서버에 저장 </center> |
| <center>GET (/paperplanes/posts)</center> | <center>서버에 있는 포스트를 불러옴</center> |
| <center>POST (/paperplanes/postin)</center> | <center>글 작성 (작성 시 token 비교)</center> |


![캡쳐1](https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/paperplanes/image1.png)

![캡쳐2](https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/paperplanes/image1.png)