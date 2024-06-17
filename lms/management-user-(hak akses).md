# How to make permission and role - _Complete management user in Laravel_
## Install and setup package [spatie](https://spatie.be/docs/laravel-permission/v6/installation-laravel)
> 1. Install your spatie with the following command ```composer require spatie/laravel-permission```.
> 2. Put the following code on your ```config/app.php```:
```
'providers' => [
    // ...
    Spatie\Permission\PermissionServiceProvider::class,
];
```
> 3. Run this artisan ```php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"``` to publish spatie/permission wich later come to ```config/permission.php```.
> 4. Run the following artisan:
```
php artisan optimize:clear
# or
php artisan config:clear
```
> 5. After that migrate your table with this artisan  ```php artisan migrate```.
> 6. Add this code to your _User model_ ```use HasRoles;```.

## CREATE PERMISSION
> 1. Add new field ```group_name, varchar(255), NULL``` on table ```permissions```.
> 2. Create controller named ```RoleController```. After that put this code in the controller:
```
<?php

namespace App\Http\Controllers\Backend;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;
use Spatie\Permission\Models\Permission;

class RoleController extends Controller
{
    public function AllPermission()
    {
        $permissions = Permission::orderBy('name','ASC')->get();
        return view('admin.backend.pages.permission.all_permission',compact('permissions'));
    }

    public function StorePermission(Request $request)
    {
        foreach(Permission::all() as $permission)
        {
            if (ucwords($permission->name) === ucwords($request->name))
            {
                $notification = array(
                    'message' => 'Permission name already exists.',
                    'alert-type' => 'error'
                );
                return back()->with($notification);
            }
        }

        Permission::create([
            'name' => $request->name,
            'group_name' => $request->group_name,
        ]);

        $notification = array(
            'message' => 'Saved.',
            'alert-type' => 'success'
        );
        return back()->with($notification);
    }

    public function EditPermission($id)
    {
        $permissions = Permission::orderBy('name','ASC')->get();
        $permissionEdit = Permission::where('id', $id)->first();
        return view('admin.backend.pages.permission.all_permission',compact('permissions','permissionEdit'));
    }

    public function UpdatePermission(Request $request)
    {
        $id = $request->id;
        DB::table('permissions')->where('id', $id)->update([
            'name' => $request->name,
            'group_name' => $request->group_name,
        ]);

        $notification = array(
            'message' => 'Saved.',
            'alert-type' => 'success'
        );

        return redirect()->route('all.permission')->with($notification);
    }

    public function DeletePermission($id)
    {
        DB::table('permissions')->where('id', $id)->delete();

        $notification = array(
            'message' => 'Deleted.',
            'alert-type' => 'success'
        );

        return back()->with($notification);
    }
}
```
> 3. Add this route to your ```routes/web.php```:
```
Route::controller(RoleController::class)->group(function(){
    Route::get('/all/permission','AllPermission')->name('all.permission');
    Route::post('/store/permission','StorePermission')->name('store.permission');
    Route::get('/edit/permission/{id}','EditPermission')->name('edit.permission');
    Route::put('/update/permission','UpdatePermission')->name('update.permission');
    Route::get('/delete/permission/{id}','DeletePermission')->name('delete.permission');
});
```
> 4. Put the following code on your blade:
```
@extends('admin.admin_dashboard')
@php
    $route = Route::current()->getName();
@endphp
@section('title')
    {{ config('app.name') }} | {{ $route == 'edit.permission' ? 'Edit Permission' :  'All Permission' }}
@endsection
@section('admin')

<div class="page-content">
    <!--breadcrumb-->
    <div class="page-breadcrumb d-none d-sm-flex align-items-center mb-3"> 
        <div class="ps-3">
            <nav aria-label="breadcrumb">
                <ol class="breadcrumb mb-0 p-0">
                    <li class="breadcrumb-item"><a href="javascript:;"><i class="bx bx-home-alt"></i></a>
                    </li>
                    @if ($route == 'edit.permission')
                        <li class="breadcrumb-item active" aria-current="page">All Permission</li>
                        <a href="{{ route('all.permission') }}" class="btn btn-primary btn-md ms-2"><i class="lni lni-arrow-left"></i>Back</a>
                    @else
                        <li class="breadcrumb-item active" aria-current="page">All Permission</li>
                    @endif
                </ol>
            </nav>
        </div>
    </div>
    <!--end breadcrumb-->

    <div class="row">
        <div class="col-12 col-lg-4">
            <div class="card radius-10 w-100">
                <div class="card-header">
                    <div class="d-flex align-items-center">
                        <h6 class="mb-0">{{ $route == 'edit.permission' ? 'Edit Permission' :  'Add New Permission' }}</h6>
                    </div>
                </div>
                <div class="card-body">

                    @if ($route == 'edit.permission')
                    <form id="myForm" action="{{ route('update.permission', ['id' => $permissionEdit->id]) }}" method="post" class="row g-3">
                        @method('put')
                        @csrf
                        
                        <input type="hidden" name="id" value="{{ $permissionEdit->id }}">
                        <div class="col-md-12">
                            <label for="name" class="form-label">Permission Name</label>
                            <input type="text" name="name" class="form-control" id="name" value="{{ $permissionEdit->name }}">
                        </div>
                        <div class="col-md-12">
                            <label for="group_name" class="form-label"> Group Name</label>
                            <select name="group_name" id="group_name" class="form-select mb-3" aria-label="Default select example">
                                <option selected disabled value="">Select group name</option>
                                <option value="Category" {{ $permissionEdit->group_name == 'Category' ? 'selected' : '' }}>Category</option>
                                <option value="Instructor" {{ $permissionEdit->group_name == 'Instructor' ? 'selected' : '' }}>Instructor </option>
                                <option value="Coupon" {{ $permissionEdit->group_name == 'Coupon' ? 'selected' : '' }}>Coupon</option>
                                <option value="Setting" {{ $permissionEdit->group_name == 'Setting' ? 'selected' : '' }}>Setting</option>
                                <option value="Orders" {{ $permissionEdit->group_name == 'Orders' ? 'selected' : '' }}>Orders</option>
                                <option value="Report" {{ $permissionEdit->group_name == 'Report' ? 'selected' : '' }}>Report</option>
                                <option value="Review" {{ $permissionEdit->group_name == 'Review' ? 'selected' : '' }}>Review</option>
                                <option value="All User" {{ $permissionEdit->group_name == 'All User' ? 'selected' : '' }}>All User </option>
                                <option value="Blog" {{ $permissionEdit->group_name == 'Blog' ? 'selected' : '' }}>Blog</option>
                                <option value="Role and Permission" {{ $permissionEdit->group_name == 'Role and Permission' ? 'selected' : '' }}>Role and Permission</option>
                            </select>
                        </div>
                        <div class="col-md-12 mb-3">
                            <div class="d-md-flex d-grid align-items-center gap-3">
                                <button type="submit" class="btn btn-primary">Update</button>
                            </div>
                        </div>
                    </form>   
                    @else
                    <form id="myForm" action="{{ route('store.permission') }}" method="post" class="row g-3">
                        @csrf
                        
                        <div class="col-md-12">
                            <label for="name" class="form-label">Permission Name</label>
                            <input type="text" name="name" class="form-control" id="name" value="{{ old('name') }}">
                        </div>
                        <div class="col-md-12">
                            <label for="group_name" class="form-label"> Group Name</label>
                            <select name="group_name" id="group_name" class="form-select mb-3" aria-label="Default select example">
                                <option selected disabled value="">Select group name</option>
                                <option value="Category">Category</option>
                                <option value="Instructor">Instructor </option>
                                <option value="Coupon">Coupon</option>
                                <option value="Setting">Setting</option>
                                <option value="Orders">Orders</option>
                                <option value="Report">Report</option>
                                <option value="Review">Review</option>
                                <option value="All User">All User </option>
                                <option value="Blog">Blog</option>
                                <option value="Role and Permission">Role and Permission</option>
                            </select>
                        </div>
                        <div class="col-md-12 mb-3">
                            <div class="d-md-flex d-grid align-items-center gap-3">
                                <button type="submit" class="btn btn-primary">Add New Permission</button>
                            </div>
                        </div>
                    </form>                        
                    @endif

                </div>
            </div>
        </div>

        <div class="col-12 col-lg-8 ">
            <div class="card radius-10 w-100">
                <div class="card-header">
                    <div class="d-flex align-items-center">
                        <h6 class="mb-0">All Permission</h6>
                    </div>
                </div>
                <div class="card-body">

                    <div class="table-responsive">
                        <table id="example" class="table table-striped table-bordered" style="width:100%">
                            <thead>
                                <tr>
                                    <th>#</th>
                                    <th>Permission Name  </th>
                                    <th>Group Name</th> 
                                    <th>Action</th>
                                </tr>
                            </thead>
                            <tbody>
                                
                                @foreach ($permissions as $key => $item) 
                                <tr>
                                    <td>{{ $key+1 }}</td>
                                    <td>{{ $item->name }}</td> 
                                    <td>{{ $item->group_name }}</td>
                                    <td>
                                    <a href="{{ route('edit.permission',$item->id) }}" class="btn btn-info"><i class="fadeIn animated bx bx-edit"></i></a>   
                                    <a href="{{ route('delete.permission',$item->id) }}" class="btn btn-danger" id="delete"><i class="fadeIn animated bx bx-trash"></i></a>                    
                                    </td>
                                </tr>
                                @endforeach
                                    
                            </tbody>
                                
                        </table>
                    </div>

                </div>
            </div>
        </div>
    </div>
</div>

<script type="text/javascript">
$(document).ready(function() {
    $('#myForm').validate({
        rules: {
            name: {
                required : true,
            },
            group_name: {
                required : true,
            },
        },
        messages :{
            name: {
                required : 'Please provide a permission name.',
            },
            group_name: {
                required : 'Please select group name.',
            }
        },
        errorElement : 'span', 
        errorPlacement: function (error,element) {
            error.addClass('invalid-feedback');
            element.closest('.col-md-12').append(error);
        },
        highlight : function(element, errorClass, validClass){
            $(element).addClass('is-invalid');
        },
        unhighlight : function(element, errorClass, validClass){
            $(element).removeClass('is-invalid');
        },
    });
});
</script>
@endsection
```
> 5. Done. You're successfully created permission. Next we'll make ```Roles```.

## CREATE ROLES
> 1. 