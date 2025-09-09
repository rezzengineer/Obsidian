```
nano /etc/apt/sources.list
apt update
apt install apache2 php libapache2-mod-php php-mysql php-intl php-zip mariadb-server phpmyadmin -y
mysql_secure _installation
-
N,Y (atur pw jadi 1) Y, N Y, Y
-
cd /var/www/html
-
wget https://github.com/PrestaShop/PrestaShop/releases/download/8.2.2/prestashop_8.2.2.zip

apt install unzip -y

unzip pr

unzip prestashop.zip

rm index.html

chown -R www-data:www-data /var/www/html

/sbin/a2enmod rewrite

systemctl restart apache2

MASUK KE CHROME SEARCH IP PHP MY ADMIN(192.168.20.2/phpMyAdmin) DAN PRESTASHOP

rm -rf install/

mv admin(tab) hanip-toko
```