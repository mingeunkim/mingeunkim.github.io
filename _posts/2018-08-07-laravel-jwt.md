---
layout: post
title: 'Laravel 5.6 RESTful API JWT'
description: 'Laravel JWT Example'
date: 2018-08-06
author: 김민근
tags: [Laravel, jwt, auth, jwt auth]
---

> 라라벨 프레임워크를 이용한 jwt 인증

## Create Project

``` bash
$ laravel new jwt-exam
$ cp .env.example .env
$ php artisan key:generate
$ php artisan serve # 개발 서버 실행
```

개발서버에서 진행 했습니다.

## JWT Setting 

``` bash
$ composer require tymon/jwt-auth
```

``` php
# config/app.php

Tymon\JWTAuth\Providers\JWTAuthServiceProvider::class,  // providers 

'JWTAuth' => Tymon\JWTAuth\Facades\JWTAuth::class, // aliases
```

```bash
$ php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\JWTAuthServiceProvider"
```

```bash
$ php artisan jwt:generate
```

에러가 발생 한다면 아래와 같이 추가 해주세요!

```php
# vendor/tymon/jwt-auth/Commands/JWTGenerateCommand.php

public function handle(){
    $this->fire();
}    
```

미들웨어에 `jwt.auth`, `jwt.refresh` 를 추가해줍니다.

```php
# app/Http/Kernel.php

'jwt.auth' => \Tymon\JWTAuth\Middleware\GetUserFromToken::class, // $routeMiddleware
'jwt.refresh' => \Tymon\JWTAuth\Middleware\RefreshToken::class, // $routeMiddleware
```

이제 jwt 를 사용하기 위한 설정은 끝났습니다.

구현할것
1. 회원가입
2. 로그인
3. 현재 사용자 정보
4. 토큰 재발행
5. 로그아웃

JWT 인증이 들어갈 컨트롤러를 생성 해줍니다.

```bash
$ php artisan make:controller JWTAuthController
```

`.env` 데이터베이스 부분을 수정 해줍니다.

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=
DB_USERNAME=
DB_PASSWORD=
```

다음 데이터베이스를 마이그레이션 해줍니다.

```bash
$ php artisan migrate
```

`migrate` 명령 도중 SQLSTATE[42000] 오류가 발생 한다면 아래와 같이 추가 해주세요.

```php
# app/Providers/AppServiceProvider.php

use Illuminate\Support\Facades\Schema;

function boot()
{
    Schema::defaultStringLength(191);
}
```

> 회원가입 구현

```php
# routes/api.php

Route::post('register', 'JWTAuthController@register');
```

유효성 검사 로직을 컨트롤러에서 구현 하는것 보단 폼 리퀘스트에서 반환 해줄것을 선호하고 있습니다.

```bash
$ php artisan make:request RegisterFormRequest
```

생성시 위치는 `app/Http/Requests/` 에 위치 하고 있습니다. 폼 리퀘스트 생성시 `authorize` 메소드와 `rules` 메소드를 기본적으로 생성 해줍니다. 아래와 같이 `rules` 메소드를 완성 해줍니다.

폼 리퀘스트에서 메세지를 추가 안하는 이유는 메세지는 블레이드 템플릿에서 확인이 가능하기에, 현재는 API 서버를 구현 하는것 이기 때문에 메세지는 X !

- 이름 : 필수적으로 요구, 문자 형식
- 이메일 : 필수적으로 요구, 이메일 형식, users 테이블에 유일하게 존재
- 비밀번호 : 필수적으로 요구, 문자 형식

```php
# app/Http/Requests/RegisterFormRequest.php

public function authorize()
{
    return true;
}

public function rules()
{
    return [
        'name' => 'required|string',
        'email' => 'required|email|unique:users',
        'password' => 'required|string'
    ];
}
```

마지막으로 `JWTAuthController` 에 `register` 메소드를 완성 해줍니다.
유저 모델 네임스페이스, 폼리퀘스트를 선언 해줍니다.

```php
use App\Http\Requests\RegisterFormRequest;
use App\User;
```


```php
# app/Http/Controllers/JWTAuthController.php

use App\Http\Requests\RegisterFormRequest; // 폼 리퀘스트
use App\User; // 유저 모델 네임스페이스
...

public function register(RegisterFormRequest $request) {
    $user = new User;
    $user->email = $request->email;
    $user->name = $request->name;
    $user->password = bcrypt($request->password);
    $user->save();
    return response([
        'status' => 'success',
        'data' => $user
    ], 200);
}
```

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/laravel-jwt/1.png">

> 로그인

```php
# routes/api.php

Route::post('login', 'JWTAuthController@login');
```

회원가입과 동일 하게 `login` 메소드를 생성해줍니다.
```php
use JWTAuth;
```
를 선언 해줍니다.

```php
# app/Http/Controllers/JWTAuthController.php

use JWTAuth;
...

public function login(Request $request) {
    $credentials = $request->only('email', 'password');
    if (! $token = JWTAuth::attempt($credentials)) {
        return response([
            'status' => 'error',
            'error' => 'invalid.credentials',
            'msg' => 'Invalid Credentials.'
        ], 400);
    } else {
        return response([
            'status' => 'success',
            'token' => $token
        ]);
    }
}
```

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/laravel-jwt/2.png">

> 현재 사용자 정보

현재 로그인 된 사용자는 발행받은 토큰으로 사용자 정보를 확인할 수 있습니다.

```php
# routes/api.php

Route::group(['middleware' => 'jwt.auth'], function(){
    Route::get('auth/user', 'JWTAuthController@user');
}
```

앞서 커널에 추가 했던 `jwt.auth` 가 익숙 한데, 로그인을 하면 `Tymon\JWTAuth\Middleware\GetUserFromToken::class` 여기서, 토큰으로부터 유저의 정보를 받아옵니다.

인증된 사용자만 `jwt.auth` 미들웨어 안에 있는 라우트에 접근이 가능합니다.
작업하던 컨트롤러에 `user` 메소드를 구현!

```php
use Auth;
```
를 선언 해줍니다.


```php
# app/Http/Controllers/JWTAuthController.php

use Auth;
...

public function user(Request $request) {
    $user = User::find(Auth::user()->id);
    return response([
        'status' => 'success',
        'data' => $user
    ]);
}
```

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/laravel-jwt/3.png">

추후에 `jwt.auth` 그룹에 로그아웃도 추가 해야합니다.

> 로그아웃

```php
# routes/api.php

Route::group(['middleware' => 'jwt.auth'], function(){
    Route::get('auth/user', 'JWTAuthController@user');
    Route::get('auth/logout', 'JWTAuthController@logout'); // 로그아웃 추가
}
```

로그아웃 메소드도 이어 만들어줍니다.

```php
# app/Http/Controllers/JWTAuthController.php

public function logout() {
    JWTAuth::invalidate();
    return response([
        'status' => 'success',
        'msg' => 'Logged out Successfully.'
    ], 200);
}
```

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/laravel-jwt/6.png">

> 토큰 재발행

```php
# routes/api.php

Route::middleware('jwt.refresh')->get('/token/refresh', 'JWTAuthController@refresh');
```

리프레시 메소드도 만들어줍니다.

모든 코드가 완성 되었습니다. `refresh` 메소드에서 재발행 해주는 코드는 `vendor/tymon/jwt-auth/src/Middleware/RefreshToken.php` 에 위치 해있습니다.

```php
# app/Http/Controllers/JWTAuthController.php

public function refresh() {
    return response([
        'status' => 'success'
    ]);
}
```

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/laravel-jwt/4.png">

토큰 재발행 

<img src="https://raw.githubusercontent.com/getsolaris/getsolaris.github.io/master/assets/images/post/laravel-jwt/5.png">

토큰 재발행 후 이전 토큰으로 발행시