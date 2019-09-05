<p align="center"><img src="https://laravel.com/assets/img/components/logo-laravel.svg"></p>

<p align="center">
<a href="https://travis-ci.org/laravel/framework"><img src="https://travis-ci.org/laravel/framework.svg" alt="Build Status"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://poser.pugx.org/laravel/framework/d/total.svg" alt="Total Downloads"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://poser.pugx.org/laravel/framework/v/stable.svg" alt="Latest Stable Version"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://poser.pugx.org/laravel/framework/license.svg" alt="License"></a>
</p>

# apiAuth
Laravel already makes it easy to perform authentication via traditional login forms, but what about APIs? APIs typically use tokens to authenticate users and do not maintain session state between requests. Laravel makes API authentication a breeze using Laravel Passport, which provides a full OAuth2 server implementation for your Laravel application.
   
To get started, install Passport via the Composer package manager:
```
composer require laravel/passport
```
The Passpor
t service provider registers its own database migration directory with the framework, so you should migrate your database after installing the package. The Passport migrations will create the tables your application needs to store clients and access tokens:
CHECK your MySql credentials in your project .env file before to run the command
```
php artisan migrate
```
Next, you should run the passport:install command. This command will create the encryption keys needed to generate secure access tokens. In addition, the command will create "personal access" and "password grant" clients which will be used to generate access tokens:
```
php artisan passport:install
```
After running this command, add the Laravel\Passport\HasApiTokens trait to your  App\User model. This trait will provide a few helper methods to your model which allow you to inspect the authenticated user's token and scopes:
```
// app/User.php
<?php
namespace App;

use Laravel\Passport\HasApiTokens;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasApiTokens, Notifiable;
    ...
    
    

```
Next, you should call the Passport::routes method within the boot method of your  AuthServiceProvider. This method will register the routes necessary to issue access tokens and revoke access tokens, clients, and personal access tokens:
```
// app/Providers/AuthServiceProvider.php
<?php
...
use Laravel\Passport\Passport;
...

class AuthServiceProvider extends ServiceProvider
{
    
    ...
    
    protected $policies = [
         // UN-comment the ModelPolicy
         'App\Model' => 'App\Policies\ModelPolicy',
    ];
    
    ...
    
    public function boot()
    {
        $this->registerPolicies();
        // Add the Routes
        Passport::routes();
    }
}
```
Finally, in your config/auth.php configuration file, you should set the driver option of the api authentication guard to passport. This will instruct your application to use Passport's TokenGuard when authenticating incoming API requests:
```
'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],

    'api' => [
        'driver' => 'passport',
        'provider' => 'users',
    ],
],
```

