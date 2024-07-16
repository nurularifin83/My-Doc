# How to install Composer in ubuntu 

## Step 1 — Installing PHP and Additional Dependencies
> 1. Update the package manager cache by running: ```sudo apt update```
> 2. Next, run the following command to install the required packages:
```
sudo apt install php-cli unzip
```
## Step 2 — Downloading and Installing Composer
> 3. Make sure you’re in your home directory, then retrieve the installer using curl:
```
cd ~
curl -sS https://getcomposer.org/installer -o /tmp/composer-setup.php
```
> 4. To facilitate the verification step, you can use the following command to programmatically obtain the latest hash from the Composer page and store it in a shell variable:
```
HASH=`curl -sS https://composer.github.io/installer.sig`
```
> 5. If you want to verify the obtained value, you can run:
```
echo $HASH
```
```Output
906a84df04cea2aa72f40b5f787e49f22d4c2f19492ac310e8cba5b96ac8b64115ac402c8cd292b8a03482574915d1a8
```
> 6. Now execute the following PHP code, as provided in the Composer download page, to verify that the installation script is safe to run:
```
php -r "if (hash_file('SHA384', '/tmp/composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
```
> 7. You’ll see the following output:
```
Installer verified
```
> 8. To install ```composer``` globally, use the following command which will download and install Composer as a system-wide command named ```composer```, under ```/usr/local/bin:```
```
sudo php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer
```
```
Output
All settings correct for using Composer
Downloading...

Composer (version 2.3.5) successfully installed to: /usr/local/bin/composer
Use it: php /usr/local/bin/composer
```
> 9. To test your installation, run:
```
composer
```

> [!IMPORTANT]
Note: If you prefer to have separate Composer executables for each project you host on this server, you can install it locally, on a per-project basis. This method is also useful when your system user doesn’t have permission to install software system-wide.
To do this, use the command php /tmp/composer-setup.php. This will generate a composer.phar file in your current directory, which can be executed with php composer.phar.

for mote please visit [this link.](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-composer-on-ubuntu-22-04)

