# How to make dynamic email configuration On Laravel
> 1. Create controller ```php artisan make:controller SettingController```.
> 2. Create model ```php artisan make:model SmtpSetting -m```. And after that.
> 3. Go to folder ```Database/migrations/2024_06_07_030008_create_smtp_settings_table.php``` and change to this:
```
public function up(): void
{
    Schema::create('smtp_settings', function (Blueprint $table) {
        $table->id();
        $table->string('mailer')->nullable();
        $table->string('host')->nullable();
        $table->string('port')->nullable();
        $table->string('username')->nullable();
        $table->string('password')->nullable();
        $table->string('encryption')->nullable();
        $table->string('from_address')->nullable();
        $table->timestamps();
    });
}
```
> 4. After that run artisan ```php artisan migrate```.
> 5. Create routes:
```
// SMPT All Route
Route::controller(SettingController::class)->group(function(){
    Route::get('/smtp/setting','SmtpSetting')->name('smtp.setting');
    Route::post('/update/smtp','SmtpUpdate')->name('update.smtp'); 
});
```
> 6. Add the following code in your controller:
```
public function SmtpSetting()
{
    $smpt = SmtpSetting::find(1);
    return view('admin.backend.setting.smpt_update',compact('smpt'));
}

public function SmtpUpdate(Request $request){

    $smtp_id = $request->id;

    SmtpSetting::find($smtp_id)->update([
        'mailer' =>  $request->mailer,
        'host' =>  $request->host,
        'port' =>  $request->port,
        'username' =>  $request->username,
        'password' =>  $request->password,
        'encryption' =>  $request->encryption,
        'from_address' =>  $request->from_address, 
    ]);

    $notification = array(
        'message' => 'Saved.',
        'alert-type' => 'success'
    );
    return back()->with($notification);  

}
```
> 7. Create blade file named ```smtp_update.blade.php```, after that paste this code (or your own html design):
```
<div class="page-content">
    <!--breadcrumb-->
    <div class="page-breadcrumb d-none d-sm-flex align-items-center mb-3"> 
        <div class="ps-3">
            <nav aria-label="breadcrumb">
                <ol class="breadcrumb mb-0 p-0">
                    <li class="breadcrumb-item"><a href="javascript:;"><i class="bx bx-home-alt"></i></a>
                    </li>
                    <li class="breadcrumb-item active" aria-current="page">Smtp Setting </li>
                </ol>
            </nav>
        </div>
         
    </div>
    <!--end breadcrumb-->
 
    <div class="card">
        <div class="card-body p-4">
            <h5 class="mb-4">Smtp Setting</h5>
            
            <form id="myForm" action="{{ route('update.smtp') }}" method="post" class="row g-3" enctype="multipart/form-data">
                @csrf

                <input type="hidden" name="id" value="{{ $smtp->id }}">

                <div class="form-group col-md-6">
                    <label for="input1" class="form-label">	Mailer</label>
                    <input type="text" name="mailer" class="form-control" id="input1" value="{{ $smtp->mailer }}"  >
                </div>

                <div class="form-group col-md-6">
                    <label for="input1" class="form-label">	Host</label>
                    <input type="text" name="host" class="form-control" id="input1" value="{{ $smtp->host }}"  >
                </div>
                <div class="form-group col-md-6">
                    <label for="input1" class="form-label">	Port</label>
                    <input type="text" name="port" class="form-control" id="input1" value="{{ $smtp->port }}"  >
                </div>
                <div class="form-group col-md-6">
                    <label for="input1" class="form-label">	Username</label>
                    <input type="text" name="username" class="form-control" id="input1" value="{{ $smtp->username }}"  >
                </div>
                <div class="form-group col-md-6">
                    <label for="input1" class="form-label">	Password</label>
                    <input type="text" name="password" class="form-control" id="input1" value="{{ $smtp->password }}"  >
                </div>
                <div class="form-group col-md-6">
                    <label for="input1" class="form-label">	Encryption</label>
                    <input type="text" name="encryption" class="form-control" id="input1" value="{{ $smtp->encryption }}"  >
                </div>
                <div class="form-group col-md-6">
                    <label for="input1" class="form-label">	From Address</label>
                    <input type="text" name="from_address" class="form-control" id="input1" value="{{ $smtp->from_address }}"  >
                </div>
                

                <div class="col-md-12">
                    <div class="d-md-flex d-grid align-items-center gap-3">
          <button type="submit" class="btn btn-primary px-4">Save Changes</button>
                      
                    </div>
                </div>
            </form>
        </div>
    </div>
</div>
```
> 8. Done. You're good to go.