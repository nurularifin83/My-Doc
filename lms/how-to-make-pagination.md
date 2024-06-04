# How to make pagination with Laravel

> 1. Add the following code in your method controller ```paginate(6)```. The complete code look like this ```$users = User::paginate(6)```.
> 2. Publish pagination file with the following artisan ```php artisan vendor:publish --tag=laravel-pagination```. However, the file you publish come to ```resources/views/vendor```.
> 3. Custome design with your own css, you can edit this file in ```resources/views/vendor/pagination/default.blade.php```.
> 4. The last one is to display the result. Put this code on your _blade_ ```{{ $courses->links('vendor.pagination.default') }}```.
> 5. Done. You're good to go now.