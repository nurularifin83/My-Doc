# How to make Live Seacrh with [Livewire](https://livewire.laravel.com/docs/quickstart) with LARAVEL
> 1. Install package ```composer require livewire/livewire```. And after that run this artisan ```php artisan make:livewire user``` to make liverwire component. Those command will generate two new files in your project:
- app/Livewire/user.php
* resources/views/livewire/user.blade.php
> 2. In your ```app/Livewire/user.php``` put the following code:
```
<?php

namespace App\Livewire;

use App\Models\User;
use Livewire\Component;

class User extends Component
{
    
    public $search = '';

    public function render()
    {
        $results = [];

        if(strlen($this->search) >= 1)
        {
            $results = User::where('name','like','%'.$this->search.'%')->get();
        }

        return view('livewire.user',[
            'users' => $results,
        ]);
    }
}
```
> 3. In your ```resources/views/livewire/user.blade.php``` put the following code:
```
<div class="card-body">
    <h3 class="card-title fs-18 pb-2">Search Field</h3>
    <div class="divider"><span></span></div>
    <form method="post">
        <div class="form-group mb-0">
            <input class="form-control form--control pl-3" type="text" wire:model.live="search" name="search" placeholder="Search courses">
            <span class="la la-search search-icon"></span>
        </div>
    </form>
    @if (sizeof($users) > 0)
    <ul>
        @foreach ($users as $user)
        <li><div class="alert alert-success">{{ $user->name }}</div></li>
        @endforeach       
    </ul>
    @endif
</div>
```
> 4. The last one is put this code ```<livewire:user />``` in your blade area, everywhere you want.
> 5. Done. You're good to go.