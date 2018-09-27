---
layout: post
title: 'Updated from Laravel 5.5 to 5.7'
description: 'Updated from Laravel 5.5 to 5.7'
date: 2018-09-27
author: Mingeun Kim
tags: [Laravel, Laravel 5.7]
---

> Simply updated from Laravel 5.5 to 5.7


Hi Artisan.

I am a student living in Korea. I hope this article is written not only for Koreans but also for you to read this guide written in English.


Below is the basic composer.json

```json
// composer.json

"require": {
    "php": ">=7.0.0",
    "fideloper/proxy": "~3.3",
    "laravel/framework": "5.5.*",
    "laravel/tinker": "~1.0",
},
```

first: proxy update
```bash
$ composer require fideloper/proxy:~4.0 -vvv
```

second: change Laravel version and proxy version
```json
// composer.json

"require": {
    "php": ">=7.1.0",
    "fideloper/proxy": "~4.0", // here ~4.0
    "laravel/framework": "5.7.*", // here 5.7.*
    "laravel/tinker": "~1.0",
},
```

third: proxy middleware change
```php
// app/Http/Middleware/TrustProxies.php

// basic 5.5
protected $headers = [
    Request::HEADER_FORWARDED => 'FORWARDED',
    Request::HEADER_X_FORWARDED_FOR => 'X_FORWARDED_FOR',
    Request::HEADER_X_FORWARDED_HOST => 'X_FORWARDED_HOST',
    Request::HEADER_X_FORWARDED_PORT => 'X_FORWARDED_PORT',
    Request::HEADER_X_FORWARDED_PROTO => 'X_FORWARDED_PROTO',
];

// Make this change. 5.7
protected $headers = Request::HEADER_X_FORWARDED_ALL;
```

its done. finally, `composer update -vvv`