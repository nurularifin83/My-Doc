# How to make send email in Laravel 10
> 1. Run this artisan ```php artisan make:mail yourfilename```. Example: ```php artisan make:mail OrderConfirm``` 
> 2. Go to folder ```app/Mail/OrderConfirm.php```. After that change ```OrderConfirm.php``` file code become like this:
```
<?php

namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Mail\Mailable;
use Illuminate\Mail\Mailables\Content;
use Illuminate\Mail\Mailables\Envelope;
use Illuminate\Queue\SerializesModels;

class OrderConfirm extends Mailable
{
    use Queueable, SerializesModels;

    /**
     * Create a new message instance.
     */
    public function __construct(private $data)
    {
        //
    }

    /**
     * Get the message envelope.
     */
    public function envelope(): Envelope
    {
        return new Envelope(
            subject: 'Your order is confirm.',
        );
    }

    /**
     * Get the message content definition.
     */
    public function content(): Content
    {
        $order = $this->data;
        return new Content(
            view: 'mail.order_mail',
            with: ['order' => $this->data],
        );
    }

    /**
     * Get the attachments for the message.
     *
     * @return array<int, \Illuminate\Mail\Mailables\Attachment>
     */
    public function attachments(): array
    {
        return [];
    }
}
```
> 3. Create folder ```mail``` in _view_, then in that folder create a blade file ```yourfilename.blade.php```. In this case i named this file as ```order_mail.blade.php```.
> 4. Design your invoice order template _(```order_mail.blade.php```)_ as you want.
> 5. In your methode controller put this code ```Mail::to($request->email)->send(new OrderConfirm($data)); ```. However, ```OrderConfirm``` is your model, maybe in your case the model is different with me.
> 6. Done. You're good to go.