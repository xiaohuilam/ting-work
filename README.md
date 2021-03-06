# ultimate-debug-tool

The perfect function based test toolkit fot PHP composer projects.

## Instsallation

```bash
composer require xiaohuilam/ultimate-debug-tool:"dev-master"
```

往 `config/app.php` 的 `providers` 添加如下

```php
Xiaohuilam\UltDebug\ServiceProvider::class,
```

## Usage

 [语法介绍](https://github.com/xiaohuilam/test-ting-work/wiki/%E8%AF%AD%E6%B3%95%E4%BB%8B%E7%BB%8D)


```php
<?php

use Xiaohuilam\UltDebug\App;
use Xiaohuilam\UltDebug\RegExp;
use Xiaohuilam\UltDebug\Store;
use Xiaohuilam\UltDebug\StoreGet;
use Xiaohuilam\UltDebug\StoreSet;
```

```php

public function YourControllerAction() {
    $debug = new App();

    $debug->appendGroup('Group 1', [
        [
            'Register' => [
                'Captcha' => [
                    'url' => '/user/captcha',
                    'data' => [ ],
                    'done' => [
                        Store::set('captcha_ticket', 'json.data.captcha_ticket'), // After success, response data will be named into `json`, and store `json.data.captcha_ticket` into captcha_ticket as this line demands.
                    ]
                ],
                'Sms' => [
                    'url' => '/user/register/sms',
                    'data' => [
                        'phone' => RegExp::make('/^1[34578][\d]{9}$/'),           // UltDebugKit will generate a mokery data for you
                        'captcha' => '1234',                                      // Static string data
                        'captcha_ticket' => Store::get('captcha_ticket'),         // Previously saved data named captcha_ticket in the step of 'Captcha'
                    ],
                    'done' => [
                        Store::set('phone', 'param.phone'),
                        Store::set('sms_ticket', 'json.data.sms_ticket'),
                    ]
                ],
                'Submit Register' => [
                    'url' => '/user/register',
                    'data' => [
                        'phone' => Store::get('phone'),
                        'password' => RegExp::make('/^[\w]{16}$/'),
                        'code' => '1234',
                        'sms_ticket' => Store::get('sms_ticket'),
                    ],
                    'done' => [
                        Store::set('uid', 'json.data.user.id'),
                        Store::set('password', 'param.password'),                 // Save data from parameters of this step-request
                    ]
                ],
            ],
            'Login' => [
                'Captcha' => [
                    'url' => '/user/captcha',
                    'data' => [ ],
                    'done' => [ Store::set('captcha_ticket', 'json.data.captcha_ticket'), ] ],
                'Submit Login' => [
                    'url' => '/user/login',
                    'data' => [ 'username' => Store::get('phone'), 'password' => Store::get('password'), 'captcha' => '1234', 'captcha_ticket' => Store::get('captcha_ticket'), ],
                    'done' => [ Store::set('authorization', 'json.data.authorization'), ] ]
            ],
        ]
    ]);

    echo $debug->render();
}

```

## Snapshot
![demo.png](https://i.loli.net/2017/08/11/598d70504135b.png)
