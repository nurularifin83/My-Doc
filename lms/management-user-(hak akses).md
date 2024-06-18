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
> 1. Add the following code to ```routes/web.php```:
```
// Roles All Route 
Route::controller(RoleController::class)->group(function(){
    Route::get('/all/roles','AllRoles')->name('all.roles');
    Route::post('/store/roles','StoreRoles')->name('roles.store');
    Route::get('/edit/roles/{id}','EditRoles')->name('edit.roles');
    Route::put('/update/roles','UpdateRoles')->name('roles.update');
    Route::get('/delete/roles/{id}','DeleteRoles')->name('delete.roles');
});
```
> 2. Add this code to the controller:
```
public function AllRoles()
{
    $roles = Role::orderBy('name','ASC')->get();
    return view('admin.backend.pages.roles.all_roles',compact('roles'));
}

public function StoreRoles(Request $request)
{
    foreach(Role::all() as $role)
    {
        if (ucwords($role->name) === ucwords($request->name))
        {
            $notification = array(
                'message' => 'Role name already exists.',
                'alert-type' => 'error'
            );
            return back()->with($notification);
        }
    }

    Role::create([
        'name' => ucwords($request->input('name')),
        'created_at' => Carbon::now()
    ]);
    $notification = array(
        'message' => 'Saved.',
        'alert-type' => 'success'
    );

    return back()->with($notification);
}

public function EditRoles($id)
{
    $roles = Role::orderBy('name','ASC')->get();
    $editRole = Role::where('id', $id)->first();
    return view('admin.backend.pages.roles.all_roles',compact('roles','editRole'));
}

public function UpdateRoles(Request $request)
{
    foreach(Role::all() as $role)
    {
        if (ucwords($role->name) === ucwords($request->name))
        {
            $notification = array(
                'message' => 'Role name already exists.',
                'alert-type' => 'error'
            );
            return back()->with($notification);
        }
    }

    $id = $request->id;
    Role::where('id',$id)->update([
        'name' => ucwords($request->input('name')),
        'updated_at' => Carbon::now()
    ]);

    $notification = array(
        'message' => 'Saved.',
        'alert-type' => 'success'
    );

    return redirect()->route('all.roles')->with($notification);
}

public function DeleteRoles($id)
{
    DB::table('roles')->where('id', $id)->delete();

    $notification = array(
        'message' => 'Deleted.',
        'alert-type' => 'success'
    );

    return back()->with($notification);
}
```
> 3. Add this code to your blade:
```
@extends('admin.admin_dashboard')
@php
    $route = Route::current()->getName();
@endphp
@section('title')
    {{ config('app.name') }} | {{ $route == 'edit.roles' ? 'Edit Role' :  'All Role' }}
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
                        <li class="breadcrumb-item active" aria-current="page">All Role</li>
                        <a href="{{ route('all.roles') }}" class="btn btn-primary btn-md ms-2"><i class="lni lni-arrow-left"></i>Back</a>
                    @else
                        <li class="breadcrumb-item active" aria-current="page">All Role</li>
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
                        <h6 class="mb-0">{{ $route == 'edit.roles' ? 'Edit Role' :  'Add New Role' }}</h6>
                    </div>
                </div>
                <div class="card-body">

                    @if ($route == 'edit.roles')
                    <form id="myForm" action="{{ route('roles.update', ['id' => $editRole->id]) }}" method="post" class="row g-3">
                        @method('put')
                        @csrf
                        
                        <input type="hidden" name="id" value="{{ $editRole->id }}">
                        <div class="col-md-12">
                            <label for="name" class="form-label">Role Name</label>
                            <input type="text" name="name" class="form-control" id="name" value="{{ $editRole->name }}">
                        </div>
                        <div class="col-md-12 mb-3">
                            <div class="d-md-flex d-grid align-items-center gap-3">
                                <button type="submit" class="btn btn-primary">Update</button>
                            </div>
                        </div>
                    </form>   
                    @else
                    <form id="myForm" action="{{ route('roles.store') }}" method="post" class="row g-3">
                        @csrf
                        
                        <div class="col-md-12">
                            <label for="name" class="form-label">Role Name</label>
                            <input type="text" name="name" class="form-control" id="name" value="{{ old('name') }}">
                        </div>
                        <div class="col-md-12 mb-3">
                            <div class="d-md-flex d-grid align-items-center gap-3">
                                <button type="submit" class="btn btn-primary">Add New Role</button>
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
                        <h6 class="mb-0">All Role</h6>
                    </div>
                </div>
                <div class="card-body">

                    <div class="table-responsive">
                        <table id="example" class="table table-striped table-bordered" style="width:100%">
                            <thead>
                                <tr>
                                    <th>#</th>
                                    <th>Role Name </th>
                                    <th>Action</th>
                                </tr>
                            </thead>
                            <tbody>
                                
                                @foreach ($roles as $key => $item) 
                                <tr>
                                    <td>{{ $key+1 }}</td>
                                    <td>{{ $item->name }}</td>
                                    <td>
                                    <a href="{{ route('edit.roles',$item->id) }}" class="btn btn-info"><i class="fadeIn animated bx bx-edit"></i></a>   
                                    <a href="{{ route('delete.roles',$item->id) }}" class="btn btn-danger" id="delete"><i class="fadeIn animated bx bx-trash"></i></a>                    
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
        },
        messages :{
            name: {
                required : 'Please provide a role name.',
            },
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
> 4. Done. Yor're good to go. Next, we'll make ```Roles in Permission```

## CREATE ROLES IN PERSMISSION
> 1. Add the following code to ```Models/User.php```:
```
/**
* Group permission
*/
public static function getpermissionGroups(){
    $permission_groups = DB::table('permissions')->select('group_name')->groupBy('group_name')->get();
    return $permission_groups;
}

/**
* Permission by group name
*/
public static function getpermissionByGroupName($group_name)
{
    $permissions = DB::table('permissions')
                    ->select('name','id','group_name')
                    ->where('group_name',$group_name)
                    ->get();
    return $permissions;
}

/**
* Role has permission
*/
public static function roleHasPermissions($role, $permissions){

    $hasPermission = true;
    foreach($permissions as $permission){
        if (!$role->hasPermissionTo($permission->name)) {
            $hasPermission = false;
            return $hasPermission;
        }
        return $hasPermission;
    }
}
```
> 2. Add route to ```routes/web.php```:
```
// Add Roles in Permission All Route 
Route::controller(RoleController::class)->group(function(){
    Route::get('/add/roles/permission','AddRolesPermission')->name('add.roles.permission');
    Route::post('/role/permission/store','StoreRolesPermission')->name('role.permission.store');
    Route::get('/all/roles/permission','AllRolesPermission')->name('all.roles.permission');
    Route::get('/admin/edit/roles/{id}','AdminEditRoles')->name('admin.edit.roles');
    Route::put('/role/permission/update/{id}','RolePermissionUpdate')->name('role.permission.update');
    Route::get('/admin/delete/roles/{id}','AdminDeleteRoles')->name('admin.delete.roles');
});
```
> 3. Add the following code to your controller:
```
public function AddRolesPermission()
{
    $roles = Role::all();
    $permissions = Permission::all();
    $permission_groups = User::getpermissionGroups();
    return view('admin.backend.pages.rolesetup.add_roles_permission',compact('roles','permissions','permission_groups'));
}

public function StoreRolesPermission(Request $request)
{
    $data = array();
    $permissions = $request->permission;

    if ($permissions == null)
    {
        $notification = array(
            'message' => 'Please select permission to save. at least one.',
            'alert-type' => 'warning'
        );

        return back()->with($notification);
    }

    foreach ($permissions as $item) {
        $data['role_id'] = $request->role_id;
        $data['permission_id'] = $item;

        DB::table('role_has_permissions')->insert($data);
    } // end foreach


    $notification = array(
        'message' => 'Role Permission Added.',
        'alert-type' => 'success'
    );
    return redirect()->route('all.roles.permission')->with($notification); 
}

public function AllRolesPermission()
{
    $roles = Role::orderBy('name','ASC')->get();
    return view('admin.backend.pages.rolesetup.all_roles_permission',compact('roles'));
}

public function AdminEditRoles($id)
{
    $role = Role::findOrFail($id);
    $permissions = Permission::all();
    $permission_groups = User::getpermissionGroups();
    return view('admin.backend.pages.rolesetup.edit_roles_permission',compact('role','permissions','permission_groups'));
}

public function RolePermissionUpdate(Request $request, $id)
{
    if ($request->permission == null)
    {
        $notification = array(
            'message' => 'Please select permission to save. at least one.',
            'alert-type' => 'warning'
        );

        return back()->with($notification);
    }

    $role = Role::findOrFail($id);

    $numericPermissionArray = [];
    foreach($request->permission as $item) {
        $numericPermissionArray[] = intval($item);
    }

    $role->syncPermissions($numericPermissionArray);

    $notification = array(
        'message' => 'Updated.',
        'alert-type' => 'success'
    );

    return redirect()->route('all.roles.permission')->with($notification);
}

public function AdminDeleteRoles($id)
{   
    $role = Role::findOrFail($id);

    if (!is_null($role)) {
        $role->delete();
    }

    $notification = array(
        'message' => 'Deleted.',
        'alert-type' => 'success'
    );

    return back()->with($notification);
}
```
> 4. Put this html code on your blade:
> _ADD BLADE_
```
@extends('admin.admin_dashboard')
@section('title')
    {{ config('app.name') }} | Roles in permission
@endsection
@section('admin')
<style>
    .form-check-label{
        text-transform: capitalize;
    }
</style>
<div class="page-content">
    <!--breadcrumb-->
    <div class="page-breadcrumb d-none d-sm-flex align-items-center mb-3"> 
        <div class="ps-3">
            <nav aria-label="breadcrumb">
                <ol class="breadcrumb mb-0 p-0">
                    <li class="breadcrumb-item"><a href="javascript:;"><i class="bx bx-home-alt"></i></a>
                    </li>
                    <li class="breadcrumb-item active" aria-current="page">Add Role In Permission</li>
                    <a href="{{ route('all.roles.permission') }}" class="btn btn-primary btn-md ms-2"><i class="lni lni-arrow-left"></i>Back</a>
                </ol>
            </nav>
        </div>
    </div>
    <!--end breadcrumb-->

    <div class="card">
        <div class="card-body p-4">
            <form id="myForm" action="{{ route('role.permission.store') }}" method="post" class="row g-3">
                @csrf

                <div class="row mt-3">
                    <div class="form-group col-md-6">
                        <label for="role_id" class="form-label"> Roles Name</label>
                        <select name="role_id" class="form-select mb-3" aria-label="Default select example">
                            <option selected disabled value="">Select Roles</option>
                             @foreach ($roles as $role) 
                             <option value="{{ $role->id }}">{{ $role->name }}</option>
                             @endforeach
                        </select>
                    </div>
                    <div class="form-group col-md-12">
                        <input class="form-check-input" type="checkbox" value="" id="flexCheckMain">
                        <label class="form-check-label" for="flexCheckMain">Permission All </label>
                    </div>
                </div>
                <hr>

                @foreach ($permission_groups as $key => $group)
                <div class="row">
                    <div class="col-3">
                        <div class="form-check">
                            <input class="form-check-input" type="checkbox" value="{{ $group->group_name }}" id="flexCheckDefault" data-id="{{ $group->group_name }}">
                            <label class="form-check-label" for="flexCheckDefault"> {{ $group->group_name }}</label>
                        </div>
                    </div>

                    <div class="col-9">
                        @php
                            $permissions = App\Models\User::getpermissionByGroupName($group->group_name)
                        @endphp
                        @foreach ($permissions as $permission)
                        <div class="form-check">
                            <input class="form-check-input" type="checkbox" name="permission[]" value="{{ $permission->id }}" data-permission="{{ $permission->group_name }}" id="checkDefault">
                            <label class="form-check-label" for="checkDefault{{ $permission->id }}">{{ $permission->name }}</label>
                        </div> 
                        @endforeach
                        <br>
                    </div>
                </div>
                {{-- // end row --}} 
                @endforeach
                
                <div class="col-md-12">
                    <div class="d-md-flex d-grid align-items-center gap-3">
                        <button type="submit" class="btn btn-primary">Save</button>
                    </div>
                </div>

            </form>
        </div>
    </div>
</div>

<script>
$('#flexCheckMain').click(function(){
    if ($(this).is(':checked')) {
        $('input[ type=checkbox]').prop('checked',true)  
    }else{
        $('input[ type=checkbox]').prop('checked',false)  
    }
});

$(document).on('click', "#flexCheckDefault", function(){
    const id = $(this).data('id');
    if ($(this).is(':checked')) {
        $("input[data-permission='"+id+"']").prop('checked',true)  
    }else{
        $("input[data-permission='"+id+"']").prop('checked',false)  
    }
});

$(document).ready(function() {
    $('#myForm').validate({
        rules: {
            role_id: {
                required : true,
            }
        },
        messages :{
            role_id: {
                required : 'Please select the role.',
            }
        },
        errorElement : 'span', 
        errorPlacement: function (error,element) {
            error.addClass('invalid-feedback');
            element.closest('.form-group').append(error);
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
> _DISPLAY DATA HTML BLADE_
```
@extends('admin.admin_dashboard')
@section('title')
    {{ config('app.name') }} | All Roles in Permission
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
                    <li class="breadcrumb-item active" aria-current="page">All Roles in Permission</li>
                    <a href="{{ route('add.roles.permission') }}" class="btn btn-primary ms-2">Add Roles in Permission </a>  
                </ol>
            </nav>
        </div>
    </div>
    <!--end breadcrumb-->

    <div class="card">
        <div class="card-body">
            <div class="table-responsive">
                <table id="example" class="table mb-0">
                    <thead class="table-light">
                        <tr>
                            <th>#</th>
                            <th>Roles Name</th>
                            <th>Permission</th>
                            <th>Actions</th>
                        </tr>
                    </thead>
                    <tbody>

                        @php
                            $i = 1;
                        @endphp
                        @foreach ($roles as $key => $item)
                            @if (count($item->permissions) > 0)
                            <tr>
                                <td>{{ $i++ }}</td>
                                <td>{{ $item->name }}</td>
                                <td>
                                    @foreach ($item->permissions as $prem)
                                    <span class="badge bg-danger">{{ $prem->name }}</span>
                                    @endforeach
                                </td>
                                <td>
                                    <div class="d-flex order-actions">
                                        <a href="{{ route('admin.edit.roles',$item->id) }}" class=""><i class='bx bxs-edit'></i></a>
                                        <a href="{{ route('admin.delete.roles',$item->id) }}" class="ms-3" id="delete"><i class='bx bxs-trash'></i></a>
                                    </div>
                                </td>
                            </tr>
                            @endif
                        @endforeach

                    </tbody>
                </table>
            </div>
        </div>
    </div>
</div>

<style type="text/css">
table#example th:nth-child(3) {
    width: 100%;
    max-width: 80%;
    word-break: break-all;
    white-space: wrap;
}

table#example td:nth-child(3){
    width: 100%;
    max-width: 80%;
    word-break: break-all;
    white-space: wrap;
}
</style>
@endsection
```
> _EDIT BLADE HTML_
```
@extends('admin.admin_dashboard')
@section('title')
    {{ config('app.name') }} | Edit Roles in permission
@endsection
@section('admin')
<style>
    .form-check-label{
        text-transform: capitalize;
    }
</style>
<div class="page-content">
    <!--breadcrumb-->
    <div class="page-breadcrumb d-none d-sm-flex align-items-center mb-3"> 
        <div class="ps-3">
            <nav aria-label="breadcrumb">
                <ol class="breadcrumb mb-0 p-0">
                    <li class="breadcrumb-item"><a href="javascript:;"><i class="bx bx-home-alt"></i></a>
                    </li>
                    <li class="breadcrumb-item active" aria-current="page">Edit Role In Permission</li>
                    <a href="{{ route('all.roles.permission') }}" class="btn btn-primary btn-md ms-2"><i class="lni lni-arrow-left"></i>Back</a>
                </ol>
            </nav>
        </div>
    </div>
    <!--end breadcrumb-->

    <div class="card">
        <div class="card-body p-4">
            <form id="myForm" action="{{ route('role.permission.update', $role->id) }}" method="post" class="row g-3">
                @method('put')
                @csrf

                <div class="row mt-3">
                    <div class="form-group col-md-6">
                        <label for="role_id" class="form-label"> Roles Name</label>
                        <h4>{{ $role->name }}</h4>
                    </div>
                    <div class="form-group col-md-12">
                        <input class="form-check-input" type="checkbox" value="" id="flexCheckMain">
                        <label class="form-check-label" for="flexCheckMain">Permission All </label>
                    </div>
                </div>
                <hr>

                @foreach ($permission_groups as $group)
                <div class="row">
                    <div class="col-3">
                        @php
                        $permissions = App\Models\User::getpermissionByGroupName($group->group_name);
                        @endphp 
                        <div class="form-check">
                            <input class="form-check-input" type="checkbox" value="{{ $group->group_name }}" id="flexCheckDefault" 
                            {{ App\Models\User::roleHasPermissions($role, $permissions) ? 'checked' : ''}} data-id="{{ $group->group_name }}">
                            <label class="form-check-label" for="flexCheckDefault"> {{ $group->group_name }}</label>
                        </div>
                    </div>

                    <div class="col-9">
                        @foreach ($permissions as $permission)
                        <div class="form-check">
                            <input class="form-check-input" type="checkbox" name="permission[]" value="{{ $permission->id }}" 
                            data-permission="{{ $permission->group_name }}" {{ $role->hasPermissionTo($permission->name) ? 'checked' : '' }} id="checkDefault">
                            <label class="form-check-label" for="checkDefault{{ $permission->id }}">{{ $permission->name }}</label>
                        </div> 
                        @endforeach
                        <br>
                    </div>
                </div>
                {{-- // end row --}} 
                @endforeach
                
                <div class="col-md-12">
                    <div class="d-md-flex d-grid align-items-center gap-3">
                        <button type="submit" class="btn btn-primary">Update</button>
                    </div>
                </div>

            </form>
        </div>
    </div>
</div>

<script>
$('#flexCheckMain').click(function(){
    if ($(this).is(':checked')) {
        $('input[ type=checkbox]').prop('checked',true)  
    }else{
        $('input[ type=checkbox]').prop('checked',false)  
    }
});

$(document).on('click', "#flexCheckDefault", function(){
    const id = $(this).data('id');
    if ($(this).is(':checked')) {
        $("input[data-permission='"+id+"']").prop('checked',true)  
    }else{
        $("input[data-permission='"+id+"']").prop('checked',false)  
    }
});

$(document).ready(function() {
    $('#myForm').validate({
        rules: {
            role_id: {
                required : true,
            }
        },
        messages :{
            role_id: {
                required : 'Please select the role.',
            }
        },
        errorElement : 'span', 
        errorPlacement: function (error,element) {
            error.addClass('invalid-feedback');
            element.closest('.form-group').append(error);
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
> 5. Done. You're good to go. [Visi this link for more](https://spatie.be/docs/laravel-permission/v6/introduction)