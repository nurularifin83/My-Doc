# Make LIVE CHAT WITH VUEJS + Laravel
## _PART I_
> 1. Install package run this command ```npm install vue@3.2.36 vue-loader@next``` and this ```npm install``` and this ```npm i @vitejs/plugin-vue```.
> 2. Make sure you successfully install the package with checked in your ```package.json``` and this code available:
```
"dependencies": {
    "vue": "^3.2.20",
    "vue-loader": "^17.3.1"
}
```
> 3. In your ```vite.config.js``` add the following code:
```
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import vue from '@vitejs/plugin-vue';

export default defineConfig({
    plugins: [ 
        laravel({
            input: [
                'resources/css/app.css',
                'resources/js/app.js',
            ],
            refresh: true,
        }),
        vue({
            template:{
                transformAssetUrls:{
                    base:null,
                    includeAbsolute: false,
                },
            },
        })
    ],
});
```
> 4. Open your ```resources/js/app.js``` and add this code:
```
import './bootstrap';
import { createApp } from 'vue/dist/vue.esm-bundler.js'; 
import SendMessage from './components/SendMessage.vue' 
import ChatMessage from './components/ChatMessage.vue' 

import Alpine from 'alpinejs';

window.Alpine = Alpine;

Alpine.start();

// Live chat
const app = createApp({
	components:{
		SendMessage, 
		ChatMessage,
	}
});
app.mount('#app');
```
> 5. Next, we make a vue file in ```resources/js/components/SendMessage.vue```.
> 6. Load file ```app.js``` in your header blade with this code ```@vite(['resources/js/app.js'])```.
> 7. In your ```SendMessage.vue``` put the following code:
```
<template>
<!-- Button trigger modal -->
<button type="button" class="nav-link active" data-toggle="modal" data-target="#exampleModal">
  Chat now
</button>

<!-- Modal -->
<div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel">Chat With {{ receivername }}</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <form id="myForm" @submit.prevent="sendMsg()">
        <div class="modal-body">
            <textarea class="form-control" name="message" v-model="form.msg" rows="3" placeholder="Type your message..."></textarea>
            <span class="text-success" v-if="succMessage.success">{{ succMessage.success }}</span>
        </div>
        <div class="modal-footer">
            <button type="submit" class="btn btn-primary">Send message</button>
        </div>
      </form>
    </div>
  </div>
</div>
</template>

<script type="text/javascript">
// Start Vue.js
import axios from 'axios';

export default {
    props: ['receiverid','receivername'],

    data() {
        return{
            form: {
                msg: "",
                receiver_id : this.receiverid,
            },
            succMessage: {},
        }
    },

    methods: {
        sendMsg() {
            if (!this.form.msg) {
                this.succMessage = "";
            } else {
                axios.post('/send-message', this.form)
                .then((res) => {
                    this.form.msg = "";
                    this.succMessage = res.data;
                }).catch(error => {
                    console.error(error);
                })
            }
        }
    }
}

$(document).ready(function() {
    $('#myForm').validate({
        rules: {
            message: {
                required : true,
            },
        },
        messages :{
            message: {
                required : 'Please provide a message',
            }
        },
        errorElement : 'span', 
        errorPlacement: function (error,element) {
            error.addClass('invalid-feedback');
            element.closest('.modal-body').append(error);
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
```
> 8. And in your blade where you want to display ui vue put this code:
```
<li class="nav-item">
    @auth
    <div id="app">
        <send-message :receiverid="{{ $instructor->id }}" receivername="{{ $instructor->name }}"></send-message>
    </div>
    @else
    <a href="{{ route('login') }}" class="nav-link active">To Start Chatting Login First</a>
    @endauth
</li>
```
> 9. After doing all above stuf run this command ```npm run build``` if you want to deploy, but if still in local just run this command ```npm run dev```, you don't need to run over and over again everytime you make a change in vue file.
> 10. Next create a controller and model with the following artisan ```php artisan make:controller ChatController``` and ```php artisan make:model ChatMessage -m```.
> 11. After create a model open your ```Database/migrations/2024_06_19_145326_create_chat_messages_table.php``` and here your table schema should be:
```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('chat_messages', function (Blueprint $table) {
            $table->id();
            $table->unsignedBigInteger('sender_id')->comment('user_id')->nullable();
            $table->unsignedBigInteger('receiver_id')->comment('instructor_id')->nullable();
            $table->text('msg');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('chat_messages');
    }
};
```
> 12. Run this artisan ```php artisan migrate``` to publish your table.
> 13. Next put this route in ```routes/web.php```:
```
// All chat message route
Route::controller(ChatController::class)->group(function() {
    Route::post('/send-message', 'SendMessage');
    Route::get('/user-all', 'GetAllUsers');
    Route::get('/user-message/{id}', 'UserMsgById');
    Route::delete('/delete-message/{id}', 'DeleteMessage');
});
Route::middleware(['auth','roles:instructor'])->group(function(){
  Route::get('/instructor/live/chat', [ChatController::class, 'LiveChat'])->name('instructor.live.chat');
});
Route::middleware(['auth','roles:user'])->group(function(){
  Route::get('/user/live/chat', [UserController::class, 'LiveChat'])->name('live.chat');
});
```
> 14. In your ```ChatController``` put the following code:
```
<?php

namespace App\Http\Controllers\Backend;

use App\Http\Controllers\Controller;
use App\Models\ChatMessage;
use App\Models\User;
use Carbon\Carbon;
use Exception;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\DB;

class ChatController extends Controller
{
    public function SendMessage(Request $request)
    {
        ChatMessage::create([
            'sender_id' => Auth::user()->id,
            'receiver_id' => $request->receiver_id,
            'msg' => $request->msg,
            'created_at' => Carbon::now(),
        ]);

        return response()->json(['success' => 'Message Send.'], 200);
    }

    public function GetAllUsers(){
        $chats = ChatMessage::orderBy('id','DESC')
                ->where('sender_id',auth()->id())
                ->orWhere('receiver_id',auth()->id())
                ->get();

        $users = $chats->flatMap(function($chat){
            if ($chat->sender_id === auth()->id()) {
                return [$chat->sender, $chat->receiver];
            }
            return [$chat->receiver, $chat->sender];
        })->unique();

        return $users;
    }

    public function UserMsgById($userId){

        $user = User::find($userId);

        if ($user) {
            $messages = ChatMessage::where(function($q) use ($userId){
                $q->where('sender_id',auth()->id());
                $q->where('receiver_id',$userId);
            })->orWhere(function($q) use ($userId){
                $q->where('sender_id',$userId);
                $q->where('receiver_id',auth()->id());
            })->with('user')->get();

            return response()->json([
                'user' => $user,
                'messages' => $messages, 
            ]);
        }else {
            abort(404);
        }
    }

    public function LiveChat(){
        $checkChat = ChatMessage::where('sender_id', auth()->id())->orWhere('receiver_id', auth()->id())->count();
        $instructor = User::where('id', Auth::user()->id)->first();
        return view('instructor.chat.live_chat', compact('instructor','checkChat'));
    }

    public function DeleteMessage($id)
    {
        DB::table('chat_messages')->where('id', $id)->delete();
        return response()->json(['message' => 'Chat deleted.']);
    }
}
```
> 15. And in your ```UserController``` put this code:
```
public function LiveChat()
{ 
    $checkChat = ChatMessage::where('sender_id', auth()->id())->orWhere('receiver_id', auth()->id())->count();
    $user = User::where('id', Auth::user()->id)->first();
    return view('frontend.dashboard.live_chat', compact('user','checkChat'));
}
```

> 16. DONE. We've done for part one. But we still log way to go. Next we'll make for part-2, in this part we'll make chat box and chat for both side.

## _PART II (for student)_ (In this part you don't to think about route and controller anymore because we've added all of that on the _first part_)
> 17. Make file in ```resources/js/components/ChatMessage.vue``` and put this code:
```
<template>
    <div class="row"  style=" width: 873px; ">
    <div class="col-md-2 myUser">
        <ul class="user">
           <strong>Chat List</strong>
           <hr>
        <li> 
          <a href="">
            <img src="/frontend/images/small-avatar-2.jpg"
              alt="UserImage"
              class="userImg"
            />
            <span class="username text-center">users</span>
          </a>
        </li>

      </ul>
    </div>
    <div class="col-md-10">
      <div class="card">
        <div class="card-header text-center myrow">
          <strong> Selected Users </strong>
        </div>
        <div class="card-body chat-msg">
          <ul class="chat">

           <li class="sender clearfix">
              <span class="chat-img left clearfix mx-2">
              <img src="/frontend/images/small-avatar-2.jpg"
                  class="userImg"
                  alt="userImg"
                />
              </span>
              <div class="chat-body2 clearfix">
                <div class="header clearfix">
                  <strong class="primary-font">Username1</strong>
                  <small class="right text-muted">
                    11:30am 
                  </small>
                  <!-- //if send with product id  -->
                  <div class="text-center">
                      product name
                  <img src="/frontend/images/sponsor-img2.png"
                      alt="productImg"
                      width="60px;"
                    />
                  </div>
                </div>

                <p>Hi..</p>
              </div>
            </li>

        <!-- my part  -->
            <li class="buyer clearfix">
              <span class="chat-img right clearfix mx-2">
                <img src="/frontend/images/small-avatar-6.jpg"
                  class="userImg"
                  alt="userImg"
                />
                 </span>
              <div class="chat-body clearfix">
                <div class="header clearfix">
                  <small class="left text-muted"
                    >12:10pm</small>
                  <!-- <strong class="right primary-font">Myusername </strong> //my name   -->
                   <div class="text-center">
                      Product name
                   <img src="/frontend/images/sponsor-img.png"
                      alt="prouductImage"
                      width="60px;"
                    />
                  </div>
                </div>
                <p>Hello...</p>
              </div>
            </li>

            <li class="sender clearfix">
              <span class="chat-img left clearfix mx-2"> </span>
            </li>

          </ul>
        </div>
        <div class="card-footer">
          <div class="input-group">
            <input
              id="btn-input"
              type="text"
              class="form-control input-sm"
              placeholder="Type your message here..."
            />
            <span class="input-group-btn">
              <button class="btn btn-primary">Send</button>
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {

}
</script>

<style> 
.username {
  color: #000;
}
.myrow{
    background: #F3F3F3;
    padding: 25px;
}
.myUser{
     padding-top: 30px;
     overflow-y: scroll;
    height: 450px;
    background: #F2F6FA;
}
.user li {
  list-style: none;
  margin-top: 20px;
 
}
.user li a:hover {
  text-decoration: none;
  color: red;
}
.userImg {
  height: 35px;
  border-radius: 50%;
}
.chat {
  list-style: none;
  margin: 0;
  padding: 0;
}
.chat li {
  margin-bottom: 40px;
  padding-bottom: 5px;
  margin-top: 20px;
  width: 80%;
  height: 10px;
}
.chat li .chat-body p {
  margin: 0;
}
.chat-msg {
  overflow-y: scroll;
  height: 350px;
  background: #F2F6FA;
}
.chat-msg .chat-img {
  width: 100px;
  height: 100px;
}
.chat-msg .img-circle {
  border-radius: 50%;
}
.chat-msg .chat-img {
  display: inline-block;
}
.chat-msg .chat-body {
  display: inline-block;
  max-width: 45%;
  margin-right: -73px; 
  background-color: #891631;
  border-radius: 12.5px;
  padding: 15px;
}
.chat-msg .chat-body2 {
  display: inline-block;
  max-width: 80%;
  margin-left: -64px;
  background-color: #080000;
  border-radius: 12.5px;
  padding: 15px;
}
.chat-msg .chat-body strong {
  color: #0169da;
}
.chat-msg .buyer {
  text-align: right;
  float: right;
}
.chat-msg .buyer p {
  text-align: left;
}
.chat-msg .sender {
  text-align: left;
  float: left;
}
.chat-msg .left {
  float: left;
}
.chat-msg .right {
  float: right;
}
.clearfix {
  clear: both;
}
</style>
```
> 18. Make a blade file named ```frontend/dashboard/live_chat.blade.php``` and put the following code:
```
@extends('frontend.dashboard.user_dashboard')
@section('title')
{{ config('app.name') }} | Live Chat
@endsection
@section('user')
<div class="tab-content">
    <div class="setting-body">
        <h3 class="fs-17 font-weight-semi-bold pb-4">Live Chat</h3>
        @if ($checkChat > 0) 
        @else
        <div class="row container"><span class="alert alert-warning">Chat will display on board if you have chat. Start chating with your favourite instructor.</span></div>
        @endif
        <div id="app">
            <chat-message role="{{ $user->role }}"></chat-message>
        </div>
    </div>
</div>
@endsection
```
> 21. Make a blade file named ```instructor/chat/live_chat.blade.php``` and put the following code:
```
@extends('instructor.instructor_dashboard')
@section('title')
{{ config('app.name') }} | Live Chat
@endsection
@section('instructor')
<div class="page-content">
    <!--breadcrumb-->
    <div class="page-breadcrumb d-none d-sm-flex align-items-center mb-3"> 
        <div class="ps-3">
            <nav aria-label="breadcrumb">
                <ol class="breadcrumb mb-0 p-0">
                    <li class="breadcrumb-item"><a href="javascript:;"><i class="bx bx-home-alt"></i></a>
                    </li>
                    @if ($checkChat > 0) 
                    <li class="breadcrumb-item active" aria-current="page">Live Chat</li>
                    @else
                    <li class="breadcrumb-item active" aria-current="page">Live Chat<span class="alert alert-warning ms-3">User will display on chat board if student chating you.</span></li>
                    @endif
                </ol>
            </nav>
        </div>
    </div>
    <!--end breadcrumb-->

    <div class="card">
        <div class="card-body">

            <div id="app">
                <chat-message role="{{ $instructor->role }}"></chat-message> 
            </div>

        </div>
    </div>
@endsection
```
> 22. Done. You're good to go.