<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400"></a></p>

<p align="center">
<a href="https://travis-ci.org/laravel/framework"><img src="https://travis-ci.org/laravel/framework.svg" alt="Build Status"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://img.shields.io/packagist/dt/laravel/framework" alt="Total Downloads"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://img.shields.io/packagist/v/laravel/framework" alt="Latest Stable Version"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://img.shields.io/packagist/l/laravel/framework" alt="License"></a>
</p>

## About Laravel

Laravel is a web application framework with expressive, elegant syntax. We believe development must be an enjoyable and creative experience to be truly fulfilling. Laravel takes the pain out of development by easing common tasks used in many web projects, such as:

- [Simple, fast routing engine](https://laravel.com/docs/routing).
- [Powerful dependency injection container](https://laravel.com/docs/container).
- Multiple back-ends for [session](https://laravel.com/docs/session) and [cache](https://laravel.com/docs/cache) storage.
- Expressive, intuitive [database ORM](https://laravel.com/docs/eloquent).
- Database agnostic [schema migrations](https://laravel.com/docs/migrations).
- [Robust background job processing](https://laravel.com/docs/queues).
- [Real-time event broadcasting](https://laravel.com/docs/broadcasting).

Laravel is accessible, powerful, and provides tools required for large, robust applications.

## Learning Laravel

Laravel has the most extensive and thorough [documentation](https://laravel.com/docs) and video tutorial library of all modern web application frameworks, making it a breeze to get started with the framework.

If you don't feel like reading, [Laracasts](https://laracasts.com) can help. Laracasts contains over 1500 video tutorials on a range of topics including Laravel, modern PHP, unit testing, and JavaScript. Boost your skills by digging into our comprehensive video library.

## Laravel Sponsors

We would like to extend our thanks to the following sponsors for funding Laravel development. If you are interested in becoming a sponsor, please visit the Laravel [Patreon page](https://patreon.com/taylorotwell).

### Premium Partners

- **[Vehikl](https://vehikl.com/)**
- **[Tighten Co.](https://tighten.co)**
- **[Kirschbaum Development Group](https://kirschbaumdevelopment.com)**
- **[64 Robots](https://64robots.com)**
- **[Cubet Techno Labs](https://cubettech.com)**
- **[Cyber-Duck](https://cyber-duck.co.uk)**
- **[Many](https://www.many.co.uk)**
- **[Webdock, Fast VPS Hosting](https://www.webdock.io/en)**
- **[DevSquad](https://devsquad.com)**
- **[OP.GG](https://op.gg)**

## Contributing

Thank you for considering contributing to the Laravel framework! The contribution guide can be found in the [Laravel documentation](https://laravel.com/docs/contributions).

## Code of Conduct

In order to ensure that the Laravel community is welcoming to all, please review and abide by the [Code of Conduct](https://laravel.com/docs/contributions#code-of-conduct).

## Security Vulnerabilities

If you discover a security vulnerability within Laravel, please send an e-mail to Taylor Otwell via [taylor@laravel.com](mailto:taylor@laravel.com). All security vulnerabilities will be promptly addressed.

## License

The Laravel framework is open-sourced software licensed under the [MIT license](https://opensource.org/licenses/MIT).


# SSO PNJ Demo (PHP, Laravel)

Repositori ini merupakan contoh implementasi penggunaan service *Single Sign-On*
Politeknik Negeri Jakarta untuk aplikasi web berbasis PHP.

#### Requirement
- Laravel Socialite
- PHP Versi 7.3 atau diatasnya
- Gunakan endpoint `client_secret_post` pada saat mendaftarkan client anda di SSO PNJ

#### Instalasi Package
Package tersedia di repo packagist, install dengan menggunakan :
```bash
composer require laravel/socialite
composer require chillrend/pnj-socialite-provider
```

### Quick Start
Jika Anda telah menambahkan `Laravel\Socialite\SocialiteServiceProvider`
didalam file `config/app.php` silahkan hapus kode tersebut yang berada di dalam array `providers[]`

Lalu tambahkan `\SocialiteProviders\Manager\ServiceProvider::class` kedalam array `providers[]`

```php
'providers' => [
    // Provider lain
    // hapus 'Laravel\Socialite\SocialiteServiceProvider', jika ada
    \SocialiteProviders\Manager\ServiceProvider::class,
];
```

Tambahkan event listener didalam file `app/Providers/EventServiceProvide`
```php
protected $listen = [
    \SocialiteProviders\Manager\SocialiteWasCalled::class => [
        // ... listener untuk provider lain ...
        'SocialiteProviders\\PNJ\\PNJExtendSocialite@handle',
    ],
];
```
Lalu tambahkan konfigurasi didalam `config/services.php`
```php
'pnj' => [    
  'client_id' => env('CLIENT_ID'),  
  'client_secret' => env('CLIENT_SECRET'),  
  'redirect' => env('REDIRECT_URI') 
],
```

dan masukkan client id, secret, dan redirect uri Anda didalam `.env`
```dotenv
CLIENT_ID=<ClientIDAnda>
CLIENT_SECRET=<ClientSecretAnda>
REDIRECT_URI=https://your-service.com/cb
```
Setelah itu Anda dapat menggunakan socialite seperti biasa.

Untuk meredirect user kedalam SSO PNJ gunakan :
```php
public function redirectToSSOPNJ(){
    return Socialite::driver('pnj')->redirect();
}
```

Untuk menangani callback redirect yang dikirim oleh SSO PNJ gunakan :
```php
public function callback(){
    // Anda akan mendapatkan token sekaligus user object disini.
    $user = Socialite::driver('pnj')->user();
}
```

Lalu Anda dapat membuat route seperti berikut :
```php
Route::get('/auth/pnj', [SSOController::class, 'redirectToSSOPNJ']);
Route::get('/cb', [SSOController::class, 'callback']);
```

Untuk meredirect user, gunakan route tersebut untuk atribut `href` pada link Anda :
```html
<a href="/auth/pnj">Login dengan SSO PNJ</a>
```

Untuk informasi implementasi lebih lanjut silahkan membaca dokumentasi laravel socialite dan repository dari provider.
https://laravel.com/docs/8.x/socialite
https://github.com/Chillrend/PNJSocialiteProvider

#### Menjalankan project demo ini
1. `composer install`
2. copy file `.env.example` dan ubah nama menjadi `.env`
3. `php artisan key:generate`
4. `php artisan serve`
5. Kunjungi http://localhost:8000/

### Report SSO PNJ Bug
email developer di `farhan@madjavacoder.com` atau submit issues didalam repositori ini.