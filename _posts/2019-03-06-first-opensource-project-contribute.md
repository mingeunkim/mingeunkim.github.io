---
layout: post
title: '사회초년생 개발자가 오픈소스 개발에 기여하기까지'
description: '00년생 개발자가 오픈소스 개발에 기여하기 까지'
comments: true
date: 2019-03-06
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
PR 날린 후 이틀 뒤 합쳐졌다고 이메일이 도착했습니다 !

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/nova-merge.png">

<br>
해당 기여가 제 생에 첫 기여였고, 다른 사람들이 제가 번역한 `Laravel Nova Localizations Korean` 을 사용 한다고 생각만 해도
정말 뿌듯합니다.

<br>
<br>

## 참여해도 될까요 ?

번역에 기여한뒤 저는 [`ModernPUG`](https://modernpug.org/) (PHP 개발자 모임) 슬랙에 참가 하였고, 모임 운영자님에게 해당 홈페이지 개발에 참여를 하고 싶다고 여쭤봤는데, 흔쾌히 수락을 하셨습니다.

<br>
<br>

## 그렇게 시작하게된 개발

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/modernpug-projects-panel.png">

운영자님께서 제게 개발 해보고 싶은것을 `Todo` 리스트 중에 골라보라고 말씀을 해주셔서, 저는 `PHP 관련 릴리즈 소식 업데이트` 를 선택 했습니다.
<br>
구현 해야할 기능들은 3가지가 있습니다. 언어는 당연히 PHP! Laravel 사용합니다.
1. PHP 관련 릴리즈 크롤링
2. 릴리즈 소식 뷰
3. Slack 알림 발송

<br>
<br>

## 개발에 대한 정리

#### 모델은 생각보다 무겁게

저는 모델은 좀 가볍게 서비스에 모든 데이터 관련 로직을 넣는 편이였는데, 해당 프로젝트에서는
모델에 데이터 관련 로직을 포함 시켰습니다.
<br>
해당 프로젝트에서의 컨셉은 아래 4가지와 같습니다.
- 가장 기본이 되는 객체로써의 존재
- 도메인 객체간의 관계를 정리한다
- 해당 객체가 가져야 하는 역할 및 속성을 같이 정리한다
- 모델은 생각보다 무거운 존재가 된다

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

#### 크롤링 정보들

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

#### 공동 프로젝트에서 주석(comment) 은 필수

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


#### 될 수 있으면 짧고 읽기 쉬운 문법을 사용합니다

기본으로 조건문을 사용하면 아래와 같은 방법으로 사용할 것 입니다.

```php
$fail = true;
$this->print('성공 건수: ' . $success);
$this->print('중복 건수: ' . $duplicate);
if ($fail) {
    $this->print('실패 건수: ' . $fail);
}
```

하지만 아래와 같이 더 줄일 수 있습니다.
<br>
릴리즈 정보 크롤링 과정에서 카운트를 아래와 같이 사용했습니다. [이곳](https://github.com/ModernPUG/modernpug.org/blob/v2/app/Services/ReleaseNews/Updater.php)을 참고 해주세요.
```php
$fail = true;
$this->print('성공 건수: ' . $success);
$this->print('중복 건수: ' . $duplicate);
$fail && $this->print('실패 건수: ' . $fail);
```

아래 코드처럼 1번 코드를 2번 같이 줄이면 코드 재사용량이 줄어듭니다.
```php
// 1
$a = $b ? $b : $c;

// 2
$a = $b ?: $c;
```


<br>
<br>

## 개발 하면서 

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/modernpug-code-review.png">

개발을 하면서 많은 코드리뷰를 통해 제 코드가 많이 다듬어졌습니다.
<br>
코드리뷰에 대해서는 [여기](http://blog.logi-spot.com/%EC%BD%94%EB%93%9C%EB%A6%AC%EB%B7%B0%EC%9D%98-%EC%A7%84%EC%A7%9C-%EB%AA%A9%EC%A0%81%EC%9D%80-%EB%94%B0%EB%A1%9C%EC%9E%88%EB%8B%A4/)를 확인해주세요.

<br>
<br>

## 코드리뷰를 하면서

코드리뷰를 하면서 코드와 네이밍에 많은 변화가 생겼습니다.
<br>
이 글을 포스팅 하면서 공유 했으면 좋겠어서 조심스럽게 올려봅니다.
<br>

#### 배열을 여러행에 걸쳐 사용할 경우 마지막 인자에 콤마를 넣습니다.

```php
class Kernel extends ConsoleKernel
{
    // 코드리뷰 전
    protected $commands = [
        CrawlFeed::class,
        CrawlRelease::class,
        PostImageUpdater::class
    ];

    // 코드리뷰 후
    protected $commands = [
        CrawlFeed::class,
        CrawlRelease::class,
        PostImageUpdater::class,
    ];
}
```


#### 한줄에 80자를 넘지 않습니다.

```php
// 코드리뷰 전
$siteUrl = $this->releaseInContent($data['post']['url'], $data['post']['before'], $data['post']['after'], $version);

// 코드리뷰 후
$siteUrl = $this->releaseInContent($data['post']['url'],
    $data['post']['before'],
    $data['post']['after'],
    $release[0],
    $data['post']['end']);
```


#### 날짜 Mutators

모델의 `$dates` 속성을 설정하면 Carbon 클래스의 인스턴스로 변경되어 사용할 수 있습니다.
<br>
사용 안한것과 `$dates` 속성의 값을 가져온것끼리 비교를 해보겠습니다.
<br>
저는 `$afterVar` 처럼 사용 했고 이번 리뷰를 통해 모델의 `$dates` 속성을 알게 되었습니다.

```php
$afterVar = strtotime($release->released_at);


class ReleaseNews extends Model
{
    protected $dates = ['released_at'];
}

$beforeVar = $release->released_at->getTimestamp();
```

위 `$beforeVar` 변수를 보면 해당 값은 Carbon 인스턴스로 캐스팅 되기 때문에 Carbon 메소드를 사용할 수 있습니다.


#### 메소드명 짓기

원래 동사를 뒤에 쓰는 편이였는데 이번 리뷰를 통해 변수와 메소드 네이밍에 대해 곰곰히 생각해보는 계기가 되었습니다.

```php
// 코드리뷰 전
private function releaseVersionCheck(string $version) {}
private function releaseDateModify(string $date) {}

// 코드리뷰 후
private function checkReleaseVersion(string $version) {}
private function convertReleaseDate(string $date) {}
```

<br>
<br>

## 정리

#### 릴리즈 뉴스 명령어

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/command-lists.png">

릴리즈 관련된 커맨드입니다. 첫번째는 크롤링이고, 두번째는 슬랙 알림 발송입니다.
라라벨 스케쥴러(crontab)가 돌면서 자동으로 수행합니다. 

```php
class Kernel extends ConsoleKernel
{
    protected function schedule(Schedule $schedule)
    {
        $schedule->command(CrawlReleaseNews::class)->hourly();
        $schedule->command(PushTodayReleaseNews::class)->dailyAt('7:05');
    }
}
```

#### 릴리즈 뉴스 크롤링

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/command-crawl.png">

```bash
$ php artisan release-news:crawl
```

하나의 릴리즈 타입당 5번을 돌며 크롤링 후 데이터베이스에 저장하게 됩니다.
<br>
릴리즈 정보 크롤링 과정에서 깃허브 릴리즈 페이지를 크롤링 해오는데, 매번 셀렉터가 변경되어
깃허브 API 를 사용했습니다.


#### 릴리즈 뉴스 슬랙 알림

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/slack-noti.png">

```bash
$ php artisan release-news:push
```

릴리즈 날짜 기준으로 어제의 데이터를 슬랙에 알림을 제공합니다.


#### 홈페이지

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/website.png">

릴리즈 정보들은 탭별로 확인이 가능합니다.
<br>
우측 위젯에 Recently Releases 부분은 각 타입(PHP, Laravel ..) 별로 최근 릴리즈만 표시 됩니다.


소스 확인은 [여기](https://github.com/getsolaris/modernpug.org)를 확인해주세요.

<br><br><br><br>

제게 다른 사람들이 코드를 평가 해주는 시간은 없었습니다.
<br>
`ModernPUG` 프로젝트를 하며 많은 코드리뷰를 받았습니다.
<br>
`'사회초년생 개발자가 오픈소스 개발에 기여하기까지'` 라는 글을 작성 하면서 다시 한번 코드리뷰의 `중요성`을 크게 뉘우쳤습니다.
코드리뷰의 좋은점은 사람마다 코드 스타일이 다르며 다른사람의 코드 작성법을 알 수 있는 길인것 같습니다.
<br>
코드리뷰를 받으며 '아 이런식으로도 가능하구나', '이런식으로 하면 더 가독성이 좋겠구나' 라는 생각을 여러번 하게 되었습니다.
<br>
또한 오픈소스가 가져오는 결과물은 다른 사람의 잘 짜여진 코드를 보고 개발 하면서 지식을 얻는것 같습니다.
<br>
<br>
<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/Screen%20Shot%202019-03-08%20at%2012.02.35%20AM.png">
<br>
이 포스팅을 19.03.07 일자에 업로드를 마쳤는데, 하루에 600분이 제 블로그에 방문 그리고 해당 포스트를 봐주셨습니다.
<br>
<br>
<br>
또한 페이스북을 통해 많은 분들이 `'사회초년생 개발자가 오픈소스 개발에 기여하기까지'` 포스트를 공유 해주셨습니다.
<br>

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/share1.png">

> OSSF 개발자 그룹

<br>
<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/share2.png">

> 국민대학교 이민석 교수님

<br>
<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/first-my-opensource-contribute/share3.png">

> 비트바이트 안서형 대표님

외에도 많은 분들이 많은 관심을 가져주셨습니다.
<br>
`'사회초년생 개발자가 오픈소스 개발에 기여하기까지'` 포스트에 관심을 가져주셔서 감사합니다. 
