## If you get the error when installing laravel like this one:
```
Your requirements could not be resolved to an installable set of packages.

  Problem 1
    - Root composer.json requires laravel/pint ^1.13 -> satisfiable by laravel/pint[v1.13.0, ..., v1.16.2].
    - laravel/pint[v1.13.0, ..., v1.16.2] require ext-xml * -> it is missing from your system. Install or enable PHP's xml extension.
  Problem 2
    - phpunit/phpunit[11.0.1, ..., 11.2.7] require ext-dom * -> it is missing from your system. Install or enable PHP's dom extension.
    - Root composer.json requires phpunit/phpunit ^11.0.1 -> satisfiable by phpunit/phpunit[11.0.1, ..., 11.2.7].

To enable extensions, verify that they are enabled in your .ini files:
    - /etc/php/8.2/cli/php.ini
    - /etc/php/8.2/cli/conf.d/10-opcache.ini
    - /etc/php/8.2/cli/conf.d/10-pdo.ini
    - /etc/php/8.2/cli/conf.d/20-calendar.ini
    - /etc/php/8.2/cli/conf.d/20-ctype.ini
    - /etc/php/8.2/cli/conf.d/20-exif.ini
    - /etc/php/8.2/cli/conf.d/20-ffi.ini
    - /etc/php/8.2/cli/conf.d/20-fileinfo.ini
    - /etc/php/8.2/cli/conf.d/20-ftp.ini
    - /etc/php/8.2/cli/conf.d/20-gettext.ini
    - /etc/php/8.2/cli/conf.d/20-iconv.ini
    - /etc/php/8.2/cli/conf.d/20-phar.ini
    - /etc/php/8.2/cli/conf.d/20-posix.ini
    - /etc/php/8.2/cli/conf.d/20-readline.ini
    - /etc/php/8.2/cli/conf.d/20-shmop.ini
    - /etc/php/8.2/cli/conf.d/20-sockets.ini
    - /etc/php/8.2/cli/conf.d/20-sysvmsg.ini
    - /etc/php/8.2/cli/conf.d/20-sysvsem.ini
    - /etc/php/8.2/cli/conf.d/20-sysvshm.ini
    - /etc/php/8.2/cli/conf.d/20-tokenizer.ini
You can also run `php --ini` in a terminal to see which files are used by PHP in CLI mode.
Alternatively, you can run Composer with `--ignore-platform-req=ext-xml --ignore-platform-req=ext-dom` to temporarily ignore these required extensions.
```
## Then run this command:
First installed ```sudo apt-get install php8.2-gd```, then ```sudo apt-get install php8.2-intl``` and last one was ```sudo apt-get install php8.2-xsl```. After that, it's installing as it should.