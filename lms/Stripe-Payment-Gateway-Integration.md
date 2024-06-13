# Stripe Payment Gateway Integration in Laravel (Option 1)
> 1. Install Stripe Package
```composer require stripe/stripe-php```
> 2. Set Up Stripe Configurations
Open your ```.env``` after that put this code :
```
STRIPE_KEY=pk_test_XXXXXXXXX
STRIPE_SECRET=sk_test_XXXXXXXXX
```
Don't forget to replace both key. After that open the ```config/services.php``` then put this code
```
'stripe' => [
    'secret' => env('STRIPE_SECRET'),
],
```
> 3. Create StripeController
Run this artisan ```php artisan make:controller StripeController```. AFter that in your controller put this code:
```
<?php
  
namespace App\Http\Controllers;
  
use Illuminate\Http\Request;
use Stripe;
  
class StripeController extends Controller
{
    public function index()
    {
        return view('checkout');
    }
     
      
    public function createCharge(Request $request)
    {
        Stripe\Stripe::setApiKey(env('STRIPE_SECRET'));
        Stripe\Charge::create ([
                "amount" => 5 * 100,
                "currency" => "usd",
                "source" => $request->stripeToken,
                "description" => "Binaryboxtuts Payment Test"
        ]);
     
        return redirect('stripe')->with('success', 'Payment Successful!');
    }
}
```
> 4. Create Blade File
In your ```Create Blade File``` create a file named ```checkout.blade.php``` then put the following code:
```
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.bundle.min.js" integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM" crossorigin="anonymous"></script>
</head>
<body>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-4">
                <div class="card">
                    <div class="card-body">
                        @if (session('success'))
                        <div 
                            style="color: green;
                                border: 2px green solid;
                                text-align: center;
                                padding: 5px;margin-bottom: 10px;">
                            Payment Successful!
                        </div>
                        @endif
                        <form id='checkout-form' method='post' action="{{url('/stripe/create-charge')}}">   
                            @csrf             
                            <input type='hidden' name='stripeToken' id='stripe-token-id'>                              
                            <label for="card-element" class="mb-5">Checkout Forms</label>
                            <br>
                            <div id="card-element" class="form-control" ></div>
                            <button 
                                id='pay-btn'
                                class="btn btn-success mt-3"
                                type="button"
                                style="margin-top: 20px; width: 100%;padding: 7px;"
                                onclick="createToken()">PAY $5
                            </button>
                        <form>
                    </div>
                </div>
            </div>
        </div>
    </div>
 
    <script src="https://js.stripe.com/v3/"></script>
    <script>
        var stripe = Stripe('{{ env('STRIPE_KEY') }}')
        var elements = stripe.elements();
        var cardElement = elements.create('card');
        cardElement.mount('#card-element');
  
        function createToken() {
            document.getElementById("pay-btn").disabled = true;
            stripe.createToken(cardElement).then(function(result) {
  
                  
                if(typeof result.error != 'undefined') {
                    document.getElementById("pay-btn").disabled = false;
                    alert(result.error.message);
                }
  
                // creating token success
                if(typeof result.token != 'undefined') {
                    document.getElementById("stripe-token-id").value = result.token.id;
                    document.getElementById('checkout-form').submit();
                }
            });
        }
    </script>
</body>
</html>
```
> 5. Create Routes
In your ```routes/web.php``` put this code:
```
Route::get('stripe', [StripeController::class, 'index']);
Route::post('stripe/create-charge', [StripeController::class, 'createCharge'])->name('stripe.create-charge');
```
> 6. Run your app ```php artisan serve```
> 7. Done. you're good to go. For more [visit this link](https://docs.stripe.com/checkout/quickstart)

# Stripe Payment Gateway Integration in Laravel (Option 2)
> 1. Install Stripe Package
```composer require stripe/stripe-php```
> 2. Create StripeController
Run this artisan ```php artisan make:controller StripeController```. AFter that in your controller put this code:
```
public function index()
{
    return view('checkout');
}

public function StripeOrder(Request $request)
{
    $stripe = new \Stripe\StripeClient('sk_test_g8r6bfvaWJeWddbyI7SUhaBC');

    if (Session::has('coupon')) 
    {
        $total_amount = Session::get('coupon')['total_amount'];
    }
    else
    {
        $total_amount = round(Cart::total());
    }

    if ($request->request_type == 'create_payment_intent')
    {
        try {

            $paymentIntent = $stripe->paymentIntents->create([
                'amount' => $total_amount*100,
                'currency' => 'usd',
                'description' => 'Stripe Payment Methode.',
                'payment_method_types' => [ 
                    'card'
                ] 
            ]);
            
            return response()->json([
                'id' => $paymentIntent->id,
                'clientSecret' => $paymentIntent->client_secret
            ]);
        } catch (Error $e) {
            return response(500)->json(['error' => $e->getMessage()]);
        }
        
    }
    elseif($request->request_type == 'create_customer')
    {
        $payment_intent_id = !empty($request->input('payment_intent_id')) ? $request->input('payment_intent_id') : ''; 
        $name = !empty($request->input('name')) ? $request->input('name') : ''; 
        $email = !empty($request->input('email')) ? $request->input('email') : ''; 
        
        // Check if PaymentIntent already has a customer 
        if(!empty($payment_intent_id)){ 
            $paymentIntent = $stripe->paymentIntents->retrieve($payment_intent_id); 
            if(!empty($paymentIntent->customer)){ 
                $customer_id = $paymentIntent->customer;
            }
        }
            
        // Add customer to stripe if not created already 
        if(empty($customer_id)){ 
            try {   
                $customer = $stripe->customers->create([ 
                    'name' => $name,  
                    'email' => $email 
                ]);  
                $customer_id = $customer->id; 
            }catch(Error $e) {   
                $api_error = $e->getMessage();
            } 
        }

        // Insert transaction data into the database 
        $order_id = Payment::insertGetId([
            'name' => $request->name,
            'email' => $request->email,
            'phone' => $request->phone,
            'address' => $request->address,
            'total_amount' => $total_amount,
            'payment_type' => 'Stripe',
            'invoice_no' => 'LMS' . mt_rand(10000000, 99999999),
            'order_date' => Carbon::now()->format('d F Y'),
            'order_month' => Carbon::now()->format('F'),
            'order_year' => Carbon::now()->format('Y'),
            'status' => 'pending',
            'created_at' => Carbon::now(), 
        ]);

        $carts = Cart::content();
        foreach ($carts as $cart) {
            Order::insert([
                'payment_id' => $order_id,
                'user_id' => Auth::user()->id,
                'course_id' => $cart->id,
                'instructor_id' => $cart->options->instructor,
                'course_title' => $cart->options->name,
                'price' => $cart->price,
                'created_at' => Carbon::now()
            ]);
        }

        if (Session::has('coupon')) {
            Session::forget('coupon');
        }
        Cart::destroy();
            
        if(empty($api_error) && !empty($customer_id)){
            try {
                // Update PaymentIntent with the customer ID 
                $paymentIntent = $stripe->paymentIntents->update($payment_intent_id, [ 
                    'customer' => $customer_id 
                ]); 
            } catch (Error $e) {  
                $api_error = $e->getMessage();  
            } 
                
            if(empty($api_error) && $paymentIntent){ 
                $paymentId = $order_id;

                // Start Send email to student
                $sendmail = Payment::find($paymentId);
                $data = [
                    'invoice_no' => $sendmail->invoice_no,
                    'amount' => $total_amount,
                    'name' => $sendmail->name,
                    'email' => $sendmail->email,
                ];

                Mail::to($request->email)->send(new OrderConfirm($data));

                return response()->json(['success' => 'Stripe Payment succeeded!']);
            }else{
                return response(500)->json(['error' => $api_error]);
            } 
        }else{
            return response(500)->json(['error' => $api_error]);
        }
    }
}
```
> 3. Create Blade File
In your ```Create Blade File``` create a file named ```checkout.blade.php``` then put the following code:
```
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="csrf-token" content="{{ csrf_token() }}">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" 
    integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.bundle.min.js" 
    integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM" crossorigin="anonymous"></script>
    <script src="https://js.stripe.com/v3/"></script>
    @vite(['resources/css/styles.css'])
</head>
<body>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-4">

              <form id='payment-form' class="hidden">
                  <input class="form-control form--control" type="hidden" id="name" name="name" value="{{ Auth::user()->name }}" required>
                  <input class="form-control form--control" type="hidden" id="email" name="email" value="{{ Auth::user()->email }}" required>
                  <input class="form-control form--control" type="hidden" id="address" name="address" value="{{ Auth::user()->address }}" required>
                  <input id="phone" class="form-control form--control" type="hidden" name="phone" value="{{ Auth::user()->phone }}" required>
                                        
                  <label for="payment-element" class="mb-5">Checkout Forms</label>
                  <br>
                  <div id="payment-element" class="form-control" >
                    <!--Stripe.js injects the Payment Element-->
                  </div>
                  <button 
                      id='submit'>
                      <div class="spinner hidden" id="spinner"></div>
                      <span id="button-text">Pay now</span>
                  </button>
                  <div id="payment-message" class="hidden text-danger"></div>
              <form>
                
                <!-- Display processing notification -->
                <div id="frmProcess" class="hidden">
                  <span class="ring"></span> Processing...
                </div>

            </div>
        </div>
    </div>
</body>
</html>
```
> 4. Add this javascript code:
```
<script>
    // Get API Key
    const stripe = Stripe('pk_test_S0Icdl8xbXRRvdI9VBw5yOGx')

    // Define card element
    let elements;
    //Select payment form element
    const paymentForm = document.querySelector('#payment-form');

    // Get payment_intent_client_secret param from URL
    const clientSecretParam = new URLSearchParams(window.location.search).get(
        "PAYMENT_INTENT_CLIENT_SECRET"
    );

    // Check whether the payment_intent_client_secret is already exist in the URL
    setProcessing(true);
    if(!clientSecretParam){
        setProcessing(false);

        // Create an instance of the Elements UI library and attach the client secret
        initialize(); 
    }

    // Check the PaymentIntent creation status
    checkStatus();

    paymentForm.addEventListener("submit", handleSubmit);

    // Fetch a payment intent and capture the client secret
    let payment_intent_id;
    async function initialize() {

        // Request option
        const requestOptions = {
            method: "POST",
            headers: { 
            "Content-Type": "application/json",
            'X-CSRF-TOKEN': '{{ csrf_token() }}',
            },
            body: JSON.stringify({ request_type:'create_payment_intent',}),
        }

        const {id, clientSecret } = await fetch("/stripe_order", requestOptions).then((r) => r.json());

        const appearance = {
            theme: 'stripe',
            rules: {
                '.Label': {
                    fontWeight: 'bold',
                    textTransform: 'uppercase',
                }
            }
        };

        elements = stripe.elements({ clientSecret, appearance });

        const paymentElementOptions = {
        layout: "tabs",
        defaultCollapsed: false,
        };

        const paymentElement = elements.create("payment", paymentElementOptions);
        paymentElement.mount("#payment-element");

        payment_intent_id = id;
    }

    // Card form submit handler
    async function handleSubmit(e) {
        e.preventDefault();
        setLoading(true);

        const name    = document.getElementById('name').value;
        const email   = document.getElementById('email').value;
        const address = document.getElementById('address').value;
        const phone   = document.getElementById('phone').value;

        // Request option
        const requestOptions = {
            method: "POST",
            headers: {
            "Content-Type": "application/json",
            'X-CSRF-TOKEN': '{{ csrf_token() }}',
            },
            body: JSON.stringify({ 
            request_type:'create_customer',
            payment_intent_id: payment_intent_id,
            name: name,
            email: email,
            address: address,
            phone: phone,
            }),
        }

        await fetch("/stripe_order", requestOptions).then((r) => r.json());

        const { error } = await stripe.confirmPayment({
        elements,
        confirmParams: {
            // Make sure to change this to your payment completion page
            return_url: "{{ route('my.course') }}",
        },
        });

        // This point will only be reached if there is an immediate error when
        // confirming the payment. Otherwise, your customer will be redirected to
        // your `return_url`. For some payment methods like iDEAL, your customer will
        // be redirected to an intermediate site first to authorize the payment, then
        // redirected to the `return_url`.
        if (error.type === "card_error" || error.type === "validation_error") {
        showMessage(error.message);
        } else {
        showMessage("An unexpected error occurred.");
        }

        setLoading(false);
    }

    // Fetches the payment intent status after payment submission
    async function checkStatus() {
        const clientSecret = new URLSearchParams(window.location.search).get(
        "PAYMENT_INTENT_CLIENT_SECRET"
        );

        if (!clientSecret) {
        return;
        }

        const { paymentIntent } = await stripe.retrievePaymentIntent(clientSecret);

        switch (paymentIntent.status) {
        case "succeeded":
            showMessage("Payment succeeded!");
            break;
        case "processing":
            showMessage("Your payment is processing.");
            break;
        case "requires_payment_method":
            showMessage("Your payment was not successful, please try again.");
            break;
        default:
            showMessage("Something went wrong.");
            break;
        }
    }

    // Message
    function showMessage(messageText) {
        const messageContainer = document.querySelector("#payment-message");

        messageContainer.classList.remove("hidden");
        messageContainer.textContent = messageText;

        setTimeout(function () {
        messageContainer.classList.add("hidden");
        messageContainer.textContent = "";
        }, 4000);
    }

    // Show a spinner on payment submission
    function setLoading(isLoading) {
        if (isLoading) {
        // Disable the button and show a spinner
        document.querySelector("#submit").disabled = true;
        document.querySelector("#spinner").classList.remove("hidden");
        document.querySelector("#button-text").classList.add("hidden");
        } else {
        document.querySelector("#submit").disabled = false;
        document.querySelector("#spinner").classList.add("hidden");
        document.querySelector("#button-text").classList.remove("hidden");
        }
    }

    // Show a spinner on payment form processing
    function setProcessing(isProcessing) {
        if (isProcessing) {
            paymentForm.classList.add("hidden");
            document.querySelector("#frmProcess").classList.remove("hidden");
        } else {
            paymentForm.classList.remove("hidden");
            document.querySelector("#frmProcess").classList.add("hidden");
        }
    }
</script>
```
> 5. Add this css code and linked to your blade via header link:
```
<style type="text/css">
/* Variables */
* {
    box-sizing: border-box;
  }
  
  body {
    font-family: -apple-system, BlinkMacSystemFont, sans-serif;
    font-size: 16px;
    -webkit-font-smoothing: antialiased;
    display: flex;
    justify-content: center;
    align-content: center;
    height: 100vh;
    width: 100vw;
  }
  
  form {
    width: 30vw;
    min-width: 500px;
    align-self: center;
    box-shadow: 0px 0px 0px 0.5px rgba(50, 50, 93, 0.1),
      0px 2px 5px 0px rgba(50, 50, 93, 0.1), 0px 1px 1.5px 0px rgba(0, 0, 0, 0.07);
    border-radius: 7px;
    padding: 40px;
  }
  
  .hidden {
    display: none;
  }
  
  #payment-message {
    color: rgb(105, 115, 134);
    font-size: 16px;
    line-height: 20px;
    padding-top: 12px;
    text-align: center;
  }
  
  #payment-element {
    margin-bottom: 24px;
  }
  
  /* Buttons and links */
  button {
    background: #5469d4;
    font-family: Arial, sans-serif;
    color: #ffffff;
    border-radius: 4px;
    border: 0;
    padding: 12px 16px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    display: block;
    transition: all 0.2s ease;
    box-shadow: 0px 4px 5.5px 0px rgba(0, 0, 0, 0.07);
    width: 100%;
  }
  button:hover {
    filter: contrast(115%);
  }
  button:disabled {
    opacity: 0.5;
    cursor: default;
  }
  
  /* spinner/processing state, errors */
  .spinner,
  .spinner:before,
  .spinner:after {
    border-radius: 50%;
  }
  .spinner {
    color: #ffffff;
    font-size: 22px;
    text-indent: -99999px;
    margin: 0px auto;
    position: relative;
    width: 20px;
    height: 20px;
    box-shadow: inset 0 0 0 2px;
    -webkit-transform: translateZ(0);
    -ms-transform: translateZ(0);
    transform: translateZ(0);
  }
  .spinner:before,
  .spinner:after {
    position: absolute;
    content: "";
  }
  .spinner:before {
    width: 10.4px;
    height: 20.4px;
    background: #5469d4;
    border-radius: 20.4px 0 0 20.4px;
    top: -0.2px;
    left: -0.2px;
    -webkit-transform-origin: 10.4px 10.2px;
    transform-origin: 10.4px 10.2px;
    -webkit-animation: loading 2s infinite ease 1.5s;
    animation: loading 2s infinite ease 1.5s;
  }
  .spinner:after {
    width: 10.4px;
    height: 10.2px;
    background: #5469d4;
    border-radius: 0 10.2px 10.2px 0;
    top: -0.1px;
    left: 10.2px;
    -webkit-transform-origin: 0px 10.2px;
    transform-origin: 0px 10.2px;
    -webkit-animation: loading 2s infinite ease;
    animation: loading 2s infinite ease;
  }
  
  @-webkit-keyframes loading {
    0% {
      -webkit-transform: rotate(0deg);
      transform: rotate(0deg);
    }
    100% {
      -webkit-transform: rotate(360deg);
      transform: rotate(360deg);
    }
  }
  @keyframes loading {
    0% {
      -webkit-transform: rotate(0deg);
      transform: rotate(0deg);
    }
    100% {
      -webkit-transform: rotate(360deg);
      transform: rotate(360deg);
    }
  }
  
  @media only screen and (max-width: 600px) {
    form {
      width: 80vw;
      min-width: initial;
    }
  }
</style>
```
> 5. Create Routes
In your ```routes/web.php``` put this code:
```
Route::get('stripe', [StripeController::class, 'index']);
Route::post('stripe/create-charge', [StripeController::class, 'StripeOrder'])->name('stripe.create-charge');
```
> 6. Run your app ```php artisan serve```
> 7. Done. you're good to go. For more [visit this link](https://docs.stripe.com/checkout/quickstart)
