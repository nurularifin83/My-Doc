# Before deploy online do this
> 1. Before zip your project to upload online, first run this artisan in your project
```
php artisan route:clear
php artisan config:cache
php artisan cache:clear
php artisan view:clear
```
> 2. Upload your project to your cpanel _File Manager_ in this folder ```/public_html/your_folder_domain_name```
> 3. In your folder ```/public_html/your_folder_domain_name/.htaccess``` add the following code:
```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} !^public
    RewriteRule ^(.*)$ public/$1 [L]
</IfModule>
```
FYI: ```Step 3``` only for sub domain. If you use main domain no need todo step 3.
> 3. Done.