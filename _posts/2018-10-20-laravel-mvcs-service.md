---
layout: post
title: 'Laravel MVCS Design Pattern Service Create Command'
description: 'Laravel MVCS Design Pattern Service Create Command'
date: 2018-10-20
author: 김민근
tags: [Laravel, mvc, mvcs, service]
---

# 라라벨 아티즌 명령어 개발 일기

기존 라라벨은 `MVC (Model-View-Controller) 패턴`이였지만 회사 첫 출근 후, 서비스 로직은 `MVCS (Model-View-Controller-Service) 패턴`이였다.

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/1da80aee3f4055d3dda05ef16068b0dbf6bb75a5/assets/images/post/laravel-mvcs/mvc.jpg">

> 위 이미지는 MVC 패턴을 설명한 이미지입니다.

MVC 패턴의 로직은 Client -> Controller -> Model -> Controller -> View 순서로 이루어져 있다.
Model 에서 Controller 로 넘어갈땐 Model단에서 가공한 데이터를 컨트롤러에 넘긴뒤 컨트롤러에서는 뷰에게 전달 해준다.

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/1da80aee3f4055d3dda05ef16068b0dbf6bb75a5/assets/images/post/laravel-mvcs/mvcs.png">

> 위 이미지는 MVCS 패턴을 설명한 이미지입니다.

원래 Controller 단에서 서비스 로직을 전부 처리한다고 할때, Service 를 도입 하면 Controller 에서 Model 과 양방향을 맺는게 아닌 Controller 단에서 Service 를 의존성 주입 (DI) 로 호출한뒤 Service에서 비즈니스 로직을 처리하는 개념이다.

회사에서 MVCS 패턴을 이용하다 보니 개인 프로젝트에도 자연스럽게 MVCS 패턴을 도입하는게 편하고, 보수가 편리했다.

기존 라라벨은 `php artisan make:controller {name}` 와 같이 컨트롤러(Controller)를 만들어주는 아티즌 명령어는 있었지만, 원래 MVC 패턴이다 보니 서비스(Service)를 만들어주는 명령어는 존재하지 않았다.

모든 프로젝트에서 일일이 서비스(Service)를 손으로 만들어주다 보면 너무 손이 많이갔다. 그래서 `php artisan make:service {name}` 명령어가 있으면 편할것 이라고 생각하고 이와 같은 명령어를 만들어보기로 생각했다.

라라벨에서 명령어를 생성하는 아티즌 명령어는 `php artisan make:command {name}` 이다. 사용자도 쉽게 아티즌 명령어를 개발할 수 있도록 라라벨에서 지원해준다.

명령어 실행 후 파일은 `app/Console/Commands/` 에 생성된다.

그 후 명령어를 개발하면 된다.

본인은 서비스를 생성하는 명령어를 개발한뒤 다른 개발자들도 사용할 수 있도록 packagist 에 업로드 했다.

`packagist` 란 ? PHP 패키지 업로드 사이트이다. 해당 사이트에 업로드를 하면 `composer` 명령어를 통해 쉽게 설치가 가능하다.


## download
- [https://github.com/getsolaris/laravel-make-service-command](https://github.com/getsolaris/laravel-make-service-command)

## reference
- MVS 이미지 : https://blog.cloudboost.io/what-is-model-view-controller-124a9942246
- MVCS 이미지 : https://glossar.hs-augsburg.de/Model-View-Controller-Service-Paradigma
