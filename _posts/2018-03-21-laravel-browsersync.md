---
layout: post
title: 'Laravel install Browsersync'
description: '라라벨 브라우저싱크 설치법'
date: 2018-03-21
author: 김민근
cover: 'https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/laravel-browsersync/running.png'
tags: [Laravel, nodejs, npm, browsersync]
---

> 라라벨 페이지 라이브 리로드 (브라우저싱크)

### 브라우저싱크란 ?
HTML, CSS, blade 파일을 변경하면 자동으로 브라우저를 업데이트합니다. Laravel-mix 를 이용하여 편하게 자신의 프로젝트에 적용할 수 있습니다.

### 설정

```php
$ npm install && npm install --save-dev browser-sync browser-sync-webpack-plugin
```

> webpack.min.js

```php
mix.browserSync({
  proxy: {
    // artisan serve시의 주소
    target: 'localhost:8000',
    reqHeaders: function() {
      // host를 직접 지정해준다.
      return {
        host: 'localhost:3000'
      };
    }
  }
});
```

webpack 까지 설정을 마친 뒤 아래 명령어를 실행 합니다.

```php
$ npm run watch
```

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/laravel-browsersync/running.png">

localhost:3000 으로 접속 가능합니다.


[출처 : 충돌 에러 해결법]('https://gracefullight.github.io/2017/08/24/Laravel-5-4-Debugbar%EC%99%80-BrowserSync%EC%9D%98-%EC%B6%A9%EB%8F%8C-%ED%95%B4%EA%B2%B0/')