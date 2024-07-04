## Change time zone to indonesia
> 1. Open File App\Providers\AppServiceProvider
Change method boot
```
use Carbon\Carbon;

public function boot()
{
	config(['app.locale' => 'id']);
	Carbon::setLocale('id');
}
```
> 2. Open file config/app.php
Change separated values
```
'timezone' => 'Asia/Jakarta',

'locale' => 'id',

'faker_locale' => 'id_ID',
```
> 3. Display the date with Indonesian language
```
Carbon\Carbon::parse('2019-03-01')->translatedFormat('d F Y');
```
> 3. Run this artisan ```php artisan config:clear```. Done.