# How to make import and export excel in Laravel
> 1. Installation. You can [visit this link](https://docs.laravel-excel.com/3.1/getting-started/installation.html) to install and set up package ```laravel-excel```. _After the installation you don't need to setup anything, except what the official say._
> 2. _CREATE EXPORT_. Run the following artisan ```php artisan make:export UsersExport --model=User```. Please suit as you want for ```User model```. However that artisan will create a file in folder ```app/Exports/UsersExport.php```. If you open you'll see the following code. In this case I just leave like that.
```
<?php

namespace App\Exports;

use App\Models\User;
use Maatwebsite\Excel\Concerns\FromCollection;

class UsersExport implements FromCollection
{
    public function collection()
    {
        return User::all();
    }
}
```
> 3. In your controller put the following code:
```
public function Export(){

    return Excel::download(new UsersExport, 'userexport.xlsx');

}
```
> 4. And finally add this route to ```routes/web.php```:
```
Route::get('users/export/', [UsersController::class, 'export']);
```

> 5. _CREAT IMPORT_. Run the following artisan ```php artisan make:import UsersImport --model=User```. Please suit as you want for ```User model```. However that artisan will create a file in folder ```app/Import/UsersImport.php```. If you open you'll see the following code. In this case I just leave like that.
```
<?php

namespace App\Imports;

use App\Models\User;
use Illuminate\Support\Facades\Hash;
use Maatwebsite\Excel\Concerns\ToModel;

class UsersImport implements ToModel
{
    /**
     * @param array $row
     *
     * @return User|null
     */
    public function model(array $row)
    {
        return new User([
           'name'     => $row[0],
           'email'    => $row[1], 
           'password' => Hash::make($row[2]),
        ]);
    }
}
```
> 3. In your controller put the following code:
```
public function import() 
{
    Excel::import(new UsersImport, 'users.xlsx');
    
    return redirect('/')->with('success', 'All good!');
}
```
> 4. And finally add this route to ```routes/web.php```:
```
Route::post('/users/import','Import')->name('import');
```