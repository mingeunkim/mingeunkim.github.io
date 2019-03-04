---
layout: post
title: '사회초년생 개발자가 오픈소스에 기여하기까지'
description: '00년생 개발자가 오픈소스에 기여하기 까지'
date: 2019-03-04
author: 김민근
tags: [Laravel, opensource, ModernPUG, PHP, Slack, Release]
---

안녕하세요. <br>해당 포스팅 제목과 같이 저는 빠르게 사회에 뛰어들은 개발자 김민근입니다.


저는 올해 특성화고 졸업을 무사히 마치고 바로 회사에 취업을 했습니다.

회사 퇴근 후 특별한 날이 아니면 자는 시간을 제외하곤 온종일 개발을 합니다.

저는 혼자 프로젝트 개발하는것을 좋아합니다.
회사일 외 개인적으로 프로젝트를 하고 싶어 혼자 프로젝트를 진행해봤지만
제 능력의 한계로 쳇바퀴돌듯 매번 그 기술을 재활용 하는 느낌이였습니다.
<br>
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
그렇게 시작된 오픈소스 여정

<br>
<br>

## 나에게 맞는 오픈소스를 찾다

우선 제일 자신 있는 `Laravel` 로 깃허브에 검색을 해가며 `help wanted` 라벨이 달린 저장소를 찾고 있었습니다.

찾는 도중 `Laravel Nova Localizations` 저장소를 발견 했고 지원하는 언어는 스무가지 넘게 있었습니다. 해당 저장소에서 제공하는 언어(Localization)는 사람들이 직접 번역을 해 Pull Request 날리고 있었습니다.
<br>
번역에 자신은 없지만 번역기의 힘을 빌려 기여를 해보기로 했습니다.

해당 저장소를 `fork` 한뒤 `en.json` 을 copy & paste `ko.json` 
<br>
key 는 기본 영어로 저는 value 만 아래 사진처럼 번역을 진행 했습니다.

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/nova-source.png">

두번째로 `Pull Request` 를 날렸습니다. 과연 날린 PR이 닫힐까, 합쳐질까 기대하고 있는 사이
PR 날린 후 이틀 뒤 합쳐졌다고 이메일(:email:)이 도착했습니다 !

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/nova-merge.png">

<br>
이때 Merge 가 되었다는걸 듣고 기분이 좋았습니다 

<br>
<br>

## 참여해도 될까요 ?

번역에 기여한뒤 저는 [`ModernPUG`](https://modernpug.org/) (PHP 개발자 모임) 슬랙에 참가 하였고, 모임 운영자님에게 해당 홈페이지 개발에 기여를 하고 싶다고 여쭤봤는데, 흔쾌히 수락을 하셨습니다.

<br>
<br>

## 그렇게 시작하게된 개발

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/modernpug-projects-panel.png">

운영자님께서 제게 하고 싶은것을 골라보라고 말씀을 해주셔서, 저는 홈페이지 개발에 할일 중 `PHP 관련 릴리즈 소식 업데이트` 를 찝었습니다.
<br>
구현 해야할 기능들은 3가지가 있습니다. 언어는 당연히 PHP! Laravel 사용합니다.
1. PHP 관련 릴리즈 크롤링
2. 릴리즈 소식 뷰
3. Slack 알림 발송

<br>
<br>

## 개발 개발 개발

#### 모델은 생각보다 무겁게

저는 모델은 좀 가볍게 서비스에 모든 데이터 관련 로직을 넣는 편이였는데, 해당 프로젝트에서는
모델에 데이터 관련 로직을 포함 시켰습니다.

```php
class ReleaseNews extends Model
{
    /**
    * @return object
    */
    static public function getReleaseNews() {
        return self::orderBy('released_at', 'desc')->get();
    }
}
```

#### 크롤링

7개 좀 넘는 정보를 제공 해주는데 모든 크롤링에 대해서는 셀렉터를 각각 지정을 해줘야 했습니다. 
<br>
저는 해당 코드를 팩토리 디자인 패턴을 이용해 리팩토링 할 예정입니다.

```php
class ReleaseNews extends Model
{
    const SUPPORT_RELEASES = [
        'PHP' => [
            'site_url'  => 'http://php.net/releases/',
            'version'   => '#layout-content > h2',
            'date'      => '#layout-content > ul > li:nth-child(1)',
            'post'      => [
                'url'       => 'http://php.net/releases/#',
                'before'    => '',
                'after'     => '',
                'end'       => ''
            ]
        ],
        'Codeigniter' => [
            'site_url'  => 'https://www.codeigniter.com/userguide3/changelog.html',
            'version'   => '#change-log > div > h2',
            'date'      => '#change-log > div > p',
            'post'     => [
                'url'      => 'https://www.codeigniter.com/userguide3/changelog.html#version-',
                'before'   => '/[. ]/',
                'after'    => '-',
                'end'      => ''
            ]
        ],
        .
        .
        .
        .
    ];
}
```

#### 주석 (comment)

저는 원래 개인 프로젝트 할때는 주석을 아예 작성하지 않는 편입니다.

하지만 달라졌습니다. PHPStorm IDE 의 기능을 이용해 주석을 아주 편하게 작성할 수 있기 때문입니다.
[해당 저장소](https://github.com/ModernPUG/modernpug.org/blob/v2/app/Services/ReleaseNews/Updater.php)를 참고해주세요.

```php
class Updater {
    /**
     * @param   string $url
     * @return  Crawler
     */
    private function convertCrawlerFromUrl(string $url) {
        $response = $this->client->get($url);
        $contents = $response->getBody()->getContents();
        $crawler = new Crawler($contents);
        return $crawler;
    }
}
```

#### ide-helper ?

Laravel 에는 IDE Helper 가 존재합니다. 자세한 내용은 [여기](https://github.com/barryvdh/laravel-ide-helper)를 참고해주세요.

해당 패키지는 정확한 자동 완성 기능을 제공합니다. 쉽게 커맨드로 phpDoc 생성이 가능합니다.

```bash
$ php artisan ide-helper:models -WR
```

```php
/**
 * App\ReleaseNews
 *
 * @property int $id
 * @property string $site_url 웹 사이트의 주소
 * @property string $type release type (Laravel, PHP, CI)
 * @property string $version release version
 * @property \Illuminate\Support\Carbon $released_at
 * @property \Illuminate\Support\Carbon|null $created_at
 * @property \Illuminate\Support\Carbon|null $updated_at
 * @method static \Illuminate\Database\Eloquent\Builder|\App\ReleaseNews newModelQuery()
 * @method static \Illuminate\Database\Eloquent\Builder|\App\ReleaseNews newQuery()
 * @method static \Illuminate\Database\Eloquent\Builder|\App\ReleaseNews query()
 * @method static \Illuminate\Database\Eloquent\Builder|\App\ReleaseNews whereCreatedAt($value)
 * @method static \Illuminate\Database\Eloquent\Builder|\App\ReleaseNews whereId($value)
 * @method static \Illuminate\Database\Eloquent\Builder|\App\ReleaseNews whereReleasedAt($value)
 * @method static \Illuminate\Database\Eloquent\Builder|\App\ReleaseNews whereSiteUrl($value)
 * @method static \Illuminate\Database\Eloquent\Builder|\App\ReleaseNews whereType($value)
 * @method static \Illuminate\Database\Eloquent\Builder|\App\ReleaseNews whereUpdatedAt($value)
 * @method static \Illuminate\Database\Eloquent\Builder|\App\ReleaseNews whereVersion($value)
 * @mixin \Eloquent
 */
```

#### 코딩 스타일 PSR-1 / PSR-2

회사 재직하면서 수많은 코드들을 보면서 코딩 스타일이 달라졌는데, 해당 프로젝트를 시작하고 많은 코드 리뷰를 받으면서 코딩 스타일도 변화하기 시작했습니다. 
저는 vscode 만 쓰다가 PHPStorm 을 해당 프로젝트 부터 사용하기 시작 했는데 (학생 라이센스 발급 받아놓고 사용을 안했네요.) 정말 신세계라는걸 느꼈습니다.
<br>
IDE 자체에서 코딩 스타일을 설정 할 수 있었고, 개발을 하다가 잘못된 문법 부분은 자동으로 고쳐줘서 편했고 즉시 어느 부분이 잘못되었는지 알 수 있었습니다.

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/phpstorm-psr.png">


PSR 에 대해서는 [여기](https://psr.kkame.net/accepted/psr-2-coding-style-guide)를 참고해주세요.


#### 조건을 줄이자

기본으로 조건문을 사용하면 아래와 같은 방법으로 사용할 것 입니다.

```php
$fail = true;
if ($fail) {
    $this->print('실패 건수: ' . $fail);
}
```

하지만 아래와 같이 더 줄일 수 있습니다.
```php
$fail = true;
$fail && $this->print('실패 건수: ' . $fail);
```

아래와 1번 코드를 2번 같이 가능합니다.
```php
// 1
$a = $b ? $b : $c;

// 2
$a = $b ?: $c;
```


#### 클로저 익명함수

<br>
<br>

## 개발 하면서 

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/modernpug-code-review.png">

개발을 하면서 많은 코드리뷰를 통해 제 코드가 많이 다듬어졌습니다.
<br>
코드리뷰에 대해서는 [여기](http://blog.logi-spot.com/%EC%BD%94%EB%93%9C%EB%A6%AC%EB%B7%B0%EC%9D%98-%EC%A7%84%EC%A7%9C-%EB%AA%A9%EC%A0%81%EC%9D%80-%EB%94%B0%EB%A1%9C%EC%9E%88%EB%8B%A4/)를 참고해주세요.