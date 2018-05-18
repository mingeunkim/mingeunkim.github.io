---
layout: post
title: '코드벤처스 팀 홈페이지'
description: '청소년 개발팀 사이트 vuejs 개발일기'
date: 2017-11-29
author: 김민근
cover: 'https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/2e3367f6f3af32bd601b7669225bb70b917473ae/assets/images/post/codeventures/logo.png'
tags: [vuejs, bootstrap]
---

> 첫 자바스크립트 프레임워크로 팀 홈페이지를 개발하다.

[사이트 바로가기 →](http://codeventures.kr)

[깃허브 바로가기 →](https://github.com/codeventures/vuejs-webserver)

### 코드벤처스란 ?
코드벤처스팀은 2017년 05월 하반기에 컨텐츠 개발을 목적으로 제 주변에서 코딩을 즐겨하는 친구들을 모아 함께 운영 하게 되었습니다. 코드벤처스의 의미는 'code' + 'adventures' 가 합쳐진뒤 'ad'가 제거된 팀이름 입니다. 기존 웹사이트에서 2017년 11월 새 시작을 해보자는 마음으로 홈페이지 리뉴얼 작업을 시작 했습니다.

### 개발 언어
- vuejs

조금의 부트스트랩을 첨가 했다.

### 뷰로 개발하게된 계기
원래는 자바스크립트 프레임워크 언어인 앵귤러를 배워보려 했다. 자바스크립트 프레임워크를 다뤄본적이 없는 나는 그나마 빠르게 적응 할 수 있다고 소문나있는 뷰를 선택 하기로 했다.

### 내가 생각하는 뷰의 장점
- 많은 문서가 정리 되어있다.
- 구현이 빠르다.
- 템플렛을 거의 그대로 사용 가능하다.

### 개발 일기
맨 처음 서버를 어디에 두어야하나 고민을 했다. 아마존 웹서비스를 이용하라는 추천을 받고 곧장 신용카드를 등록해 Ubuntu 서버를 프리티어로 1년동안 무료로 이용할 수 있게 되었다. 처음 자바스크립트 프레임워크를 써보는 나에겐 새로고침 없이 에디터에서 수정하면 바로 동기화 되는것이 신세계였다. 찾아보니 webpack pre-loader 때문이라고 한다.(틀릴 수 도 있음... 아직까지도 햇갈림..) 에디터에서 수정하면 새로고침 없이 바로 동기화 되는것이 원동력이 되어 개발 시간을 단축 시켜주었다. 사이트 내 포스팅존 이나 개발자들에서 볼 수 있는 이쁜 폼은 약 3천원 가량의 돈을 지불 하고 구매하였다. (돈을 내고 구매한것에 후회가 없음.. 정말 이쁘기 때문에!) 코드벤처스 화이팅! 

### 해결
1. codeventures.kr/#/about 과 같이 주소 뒤에 해시태그가 자동으로 붙는다. 나는 해시태그를 제거 하고 싶었다.
- router 부분에 mode: history 를 추가해줌으로써 해시태그가 제거된다.


```javascript
// 참고 링크 : https://router.vuejs.org/kr/essentials/history-mode.html

import Vue from 'vue'
import Router from 'vue-router'


import Home from '@/components/Home'
import Home2 from '@/components/Home2'


Vue.use(Router)

export default new Router({
  mode: 'history', // 추가
    routes: [
        {
            path: '/',
            name: 'Home',
            component: Home
        },
        {
            path: '/home2',
            name: 'Home2',
            component: Home2
        }
        
    ]
});

```


### 사이트

![캡쳐1](https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/codeventures/image1.png)

![캡쳐2](https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/codeventures/image2.png)