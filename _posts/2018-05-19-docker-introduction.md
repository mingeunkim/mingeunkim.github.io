---
layout: post
title: 'Docker & Laravel (Laradock) 사용해보다..'
description: '도커를 이용해 하이스쿨 스토어를 서비스 해보기 시작편'
date: 2018-05-19
author: 김민근
tags: [Docker, Laravel, Laradock, nginx, mariadb]
---

> 도커를 이용한 서버 관리

<img src="https://ih1.redbubble.net/image.386900865.0087/flat,800x800,075,f.jpg" width="500">

### 도커 입문 계기
기존 하이스쿨 스토어는 apache2 + php7.1 + mysql 편하게 말하면 apm 기술을 사용하고 있었다. 하지만 라라벨 이용자들의 말을 들어보면 아파치 보다는 엔진엑스가 성능이 더 좋고
라라벨을 받쳐준다는 말을 듣고 서버를 갈아 엎기 시작했다. nginx ... php7.2 ... mariadb ... 이 세가지를 설치하고 나면 어느 하나가 오류가 나기 시작했다.


서버를 효율적으로 관리하기위해 구글링을 열심히 해보는중 ```Docker``` 라는 단어가 눈에 띄었다.


복잡하고 어려운 서버관리대신 손쉬운 서버관리를 선택하는건 ```당연```했다.

### 서버 운영이 쉬워진다 ?
1. 이미지만 다운받고 컨테이너만 생성하면 된다.
2. 서버의 환경이 바뀌어도 의존성 문제 없이 이미지 다운 -> 컨테이너 생성 로직


### Laradock 이란 ?
라라독([Laradock](https://github.com/laradock/laradock/)) 은 도커 컨테이너에서 돌아가는 PHP 개발환경이다.

### Laradock 사용

```
$ git clone https://github.com/Laradock/laradock.git
$ cp env-sample .env
```
.environment(env) 파일에는 laradock 에서 제공하는 이미지들의 환경 값이 들어가있다.

제공해주는 이미지들은 수없이 많으며 많은 이미지들중 아래 이미지를 선택했다.

- nginx
- mariadb
- phpmyadmin
- workspace

```
$ docker-compose up -d nginx mariadb phpmyadmin workspace
$ docker ps
```

```http://localhost``` 에 접속을 하게되면 nginx 초기 화면이 보일것이다.

nginx 설정파일을 수정해 기본 접속시 내 프로젝트가 보이게 설정해보자.

```vi laradock/nginx/sites/default.conf```

```
root /var/www/public;
```

이 부분을 아래와 같이 자신의 디렉터리로 설정해준다.

```
root /var/www/myProject/public;
```


실행중인 도커 프로세스를 확인해보자. `nginx` `workspace` `php-fpm` `maria-db` 가 실행중이다.
`php-fpm` 은 `docker-compose up` 을 안해도 자동으로 생성되는 컨테이너이다.

```
$ docker ps
```

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/docker/1.png">


새로운 라라벨 프로젝트를 생성한다.

```
$ composer create-project --prefer-dist laravel/laravel docker-laravel -vvv
```

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/docker/2.png">


workspace 에 접속하여 자신의 프로젝트 파일을 설정 해준다.

글에서는 docker-laravel 이지만 `myProject` 에는 자신의 라라벨 프로젝트명이 와야함...

```
$ docker-compose exec workspace bash
$ cd myProject
$ cp .env.sample .env
$ php artisan key:generate
```

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/docker/3.png">


`http://localhost` 에 접속해보자. 라라벨 화면이 뜬다.

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/docker/4.png">

이제는 mariadb 를 건들여보자. docker workspace 에 연결중이라면 `exit` 를 쳐서 밖으로 빠져나온다.


따로 laradock .env 를 건들지 않은 상태에서는 mariadb(mysql) 의 초기 비밀번호는 root 이다.

```
$ docker-compose exec mariadb bash
# mysql -uroot -p
```

잘 접속된다. 

프로젝트에 쿼리를 연결 하려면 `myProject/.env` 를 수정하면된다.

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=myproject
DB_USERNAME=root
DB_PASSWORD=root
```

비밀번호는 꼭 변경하도록 하자.

감사합니다.