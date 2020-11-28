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
