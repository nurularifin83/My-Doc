# How to install PHP
1. Run system updates
```
sudo apt update && apt upgrade -y
```
2. Add Ondrej sury PPA repository
```
sudo add-apt-repository ppa:ondrej/php
```
after that updated
```
sudo apt update
```
3. Install PHP 8.2 on Ubuntu 22.04
```
sudo apt install php8.2 -y
```
To check if php already installed
```
php --version
```
4. Install PHP 8.2 Extensions
```
sudo apt-get install php8.2-PACKAGE_NAME
```
The complete code ready to install
```
sudo apt-get install -y php8.2-cli php8.2-common php8.2-fpm php8.2-mysql php8.2-zip php8.2-gd php8.2-mbstring php8.2-curl php8.2-xml php8.2-bcmath
```
5. If you already install many php version, you can select wich one do you want to use:
```
sudo update-alternatives --config php
```