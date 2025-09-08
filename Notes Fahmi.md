### Jika kalo tidak ada internet 
#### 1. Masukkan cd lalu
```
apt-cdrom add
apt install openssh-server -y
nano /etc/resolv.conf
-
#Tambah kan nameserver :8.8.8.8 dan nameserver 1.1.1.1
nano /etc/apt/sources.list
Masukkan repository di putty 
-
apt update
Lalu lanjutkan
~~~~~~~~~~~~~~~~~~~~~~~
apt install unzip wget curl sudo -y
apt install apache2 MariaDB-server php-curl php-zip php-intl php-mysql phpMyAdmin libapache2-mod-php -y
sudo a2enmod rewrite
mysql_secure_installation
sudo mariadb 
create database prestashop;
cd /var/www/html
wget https://github.com/PrestaShop/PrestaShop/releases/download/8.2.2/prestashop_8.2.2.zip
unzip prestashop_8.2.2.zip
unzip prestashop.zip
rm -rf index.html
chown -R www-data:www-data /var/www/html
systemctl restart apache2
rm -rf /var/www/html/install/
mv admin fahmi/
```
