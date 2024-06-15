# How to make ```user-online``` in Laravel
> 1. Add column named ```last_seen, varchar(255), NULL``` in your ```users``` table.
> 2. Open file ```app/Http/Middleware/RedirectIfAuthenticated.php``` and add the following code:
```
if (Auth::check()) {
    $expireTime = Carbon::now()->addSeconds(30);
    Cache::put('user-is-online' . Auth::user()->id, true, $expireTime);
    User::where('id',Auth::user()->id)->update(['last_seen' => Carbon::now()]);
}
```
> 3. Open file ```app/Models/User.php``` and add the following code:
```
// User Active Now
public function UserOnline(){
    return Cache::has('user-is-online' . $this->id);
}
```
> 4. Create a controller with this artisan ```php artisan make:controller ActiveUserController```.
> 5. Add this route to your ```routes/web.php```:
```
Route::get('/all/online/user',[ActiveUserController::class,'AllUser']);
```
> 6. Add the following code in your ```ActiveUserController``` controller:
```
public function AllUser()
{
    $users = User::latest()->get();
    return view('user_all',compact('users'));
}
```
> 7. Make blade file ```resources/views/user_all.blade.php```.
> 8. Display your ```last_seen``` in your blade like the following code:
```
@foreach ($users as $key => $item) 
<tr>
    <td>
        @if ($item->UserOnline())
        <span class="badge badge-pill bg-success">Active Now</span>
        @else 
        <span class="badge badge-pill bg-danger">{{ Carbon\Carbon::parse($item->last_seen)->diffForHumans() }} </span>      
        @endif    
    </td>                            
</tr>
@endforeach
```
> 5. Done. You're good to go.