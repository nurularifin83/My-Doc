# How to make notification on Laravel
> 1. Run the following artisan ```php artisan notifications:table``` & ```php artisan migrate```.
> 2. Run the following artisan to make file for notification. However this file we use to set the message: ```php artisan make:notification OrderComplete```. And after that change the default code to the following code (suit to your code) in file ```App/Notificaitons/OrderComplete.php```:
```
<?php

namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class OrderComplete extends Notification
{
    use Queueable;

    protected $orderData;

    /**
     * Create a new notification instance.
     */
    public function __construct($orderData)
    {
        $this->orderData = $orderData;
    }

    /**
     * Get the notification's delivery channels.
     *
     * @return array<int, string>
     */
    public function via(object $notifiable): array
    {
        return ['database'];
    }

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
                    ->line('The introduction to the notification.')
                    ->action('Notification Action', url('/'))
                    ->line('Thank you for using our application!');
    }

    /**
     * Get the array representation of the notification.
     *
     * @return array<string, mixed>
     */
    public function toArray(object $notifiable): array
    {
        return [
            'name' => $this->orderData['name'],
            'image' => $this->orderData['image'],
        ];
    }
}
```
> 3. Change the default code to the following code ```app/Providers/AppServiceProvider.php```:
```
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Notifications\DatabaseNotification;
use Illuminate\Support\Facades\View;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     */
    public function register(): void
    {
        //
    }

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        // Make data notification can display everywhere
        $notifications = DatabaseNotification::latest()->get();
        View::share(['notifications' => $notifications]);
    }
}

```
> 4. Create a model with the following artisan ```php artisan make:model Order -m```, after that add ```Notifiable``` in your model:
```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Notifications\Notifiable;

class Order extends Model
{
    use HasFactory, Notifiable;

    protected $guarded = [];
}
```
> 5. Create a controller ```php artisan make:controller CartController```. And put this code in your _CartController_:
```
public function Payment(Request $request)
{
    $orderNotification = Order::where('user_id', Auth::user()->id)->where('course_id', $request->course_id[$key])->first();
    $orderData = [
    'name' => $orderNotification->user->name,
    'image' => $orderNotification->user->profile_photo_path
    ];
    
    // Send Notification
    $orderNotification->notify(new OrderComplete($orderData));
}

public function MarkAsRead($notificationId)
{
    $notification = DB::table('notifications')->where('notifiable_type','=','App\Models\Order')->where('id',$notificationId);
    $unreadNotifications = DB::table('notifications')->where('notifiable_type','App\Models\Order')->whereNull('read_at')->count();
    
    $notification->update([
        'read_at' => Carbon::now()
    ]);

    return response()->json(['count' => $unreadNotifications]);
}
```
> 6. Make the route ```routes/web.php```:
```
Route::post('/payment/', [CartController::class, 'Payment']);
Route::post('/mark-notification-as-read/{notification}', [CartController::class, 'MarkAsRead']);
```
> 7. Put this html code in your blade:
```
@php
    $countUnread = $notifications->where('notifiable_type','App\Models\Order')->whereNull('read_at')->count();
@endphp
<li class="nav-item dropdown dropdown-large">
    <a class="nav-link dropdown-toggle dropdown-toggle-nocaret position-relative" href="#" data-bs-toggle="dropdown">
        <span class="alert-count" id="notification-count">{{ $countUnread }}</span>
        <i class='bx bx-bell'></i>
    </a>
    <div class="dropdown-menu dropdown-menu-end">
        <a href="javascript:;">
            <div class="msg-header">
                <p class="msg-header-title">Notifications</p>
                @if ($countUnread == 0)
                @else
                <p class="msg-header-badge"><span id="sub-notification-count">{{ $countUnread }}</span> Unread</p>
                @endif
            </div>
        </a>
        
        <div class="header-notifications-list reload-notification"> 
            @forelse ($notifications->where('notifiable_type','App\Models\Order')->take(10) as $notification) 
            <a class="dropdown-item {{ !empty($notification->read_at) ? '' : 'bg-defaultnotif' }}" href="javascript:;" onclick="markNotificationRead('{{ $notification->id }}')">
                <div class="d-flex align-items-center">
                    <div class="notify bg-light-success text-success">
                        <img src="{{ !empty(json_decode(json_encode($notification->data), true)['image']) ? 
                        asset(json_decode(json_encode($notification->data), true)['image']) : url('upload/no_image.png') }}" alt="user avatar" width="25">
                    </div>
                    <div class="flex-grow-1">
                        <h6 class="msg-name">{{ json_decode(json_encode($notification->data), true)['name'] }}<span class="msg-time float-end">{{ Carbon\Carbon::parse($notification->created_at)->diffForHumans() }}</span></h6>
                        <p class="msg-info">You have recived new orders</p>
                    </div>
                </div>
            </a>
            @empty
            <div class="d-flex align-items-center">
                <a class="dropdown-item" href="javascript:;">
                    <div class="d-flex align-items-center">
                        <div class="flex-grow-1">
                            <h3 class="msg-info">Notifications empty.</h3>
                        </div>
                    </div>
                </a>
            </div>
            @endforelse
        </div>
    </div>
</li>

<style type="text/css">
.bg-defaultnotif{
    background: #f7f7ff
}
</style>
```
> 8. Put the following javascript code in your blade below html code:
```
<script>
function markNotificationRead(notificationId){

    fetch('/mark-notification-as-read/'+notificationId,{
        method: 'POST',
        headers: {
            'Content-Type' : 'application/json',
            'X-CSRF-TOKEN' : '{{ csrf_token() }}'
        },
        body: JSON.stringify({})
    })
    .then(response => response.json())
    .then(data => {
        const notificationCount = document.getElementById('notification-count').innerText;
        if(notificationCount > 0){
            document.getElementById('notification-count').textContent = data.count-1;
            document.getElementById('sub-notification-count').textContent = data.count-1;
            $('.reload-notification').load(location.href + " .reload-notification>*","");
        }        
    })
    .catch(error => {
        console.log('Error',error)
    });
}
</script>
```
> 9. Done. You're good to go. For more [visit this link](https://laravel.com/docs/11.x/notifications)