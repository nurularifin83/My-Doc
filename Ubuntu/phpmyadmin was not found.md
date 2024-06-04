# The requested URL /phpmyadmin was not found on this server

> ```sudo -H gedit /etc/apache2/apache2.conf```
> Then add the following line to the end of the file: ```Include /etc/phpmyadmin/apache.conf```
> Then restart apache: type this code in terminal ```/etc/init.d/apache2 restart```