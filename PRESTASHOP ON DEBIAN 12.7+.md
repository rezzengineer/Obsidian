```
su -
#Masukkan repository
nano /etc/apt/sources.list/ 
apt update
apt-get install apt-transport-https ca-certificates -y
apt install unzip wget curl sudo -y
apt install apache2
apt install mariadb-server
apt install php-cli php-gd php-mbstring php-xml php-xmlrpc php-curl php-zip php-intl phpmyadmin libapache2-mod-php -y
sudo a2enmod rewrite
mysql_secure_installation
sudo mariadb
```

```
#dalam mariadb
CREATE DATABASE prestashop;
CREATE USER 'sql_kasir'@'localhost' IDENTIFIED BY'sql_kasir';
GRANT ALL PRIVILEGES ON prestashop.* TO 'sql_kasir'@'localhost';
```

```
cd /var/www/html/
wget https://github.com/PrestaShop/PrestaShop/releases/download/8.2.2/prestashop_8.2.2.zip
unzip prestashop(pencet tab aja)
unzip prestashop.zip 
#Nanti ada pilihan replace index.php silahkan ketik Y atau A
rm -rf index.html
chown -R www-data:www-data /var/www/html/
```

```
cd /etc/apache2/sites-available/
nano 000-default.conf
```
Silahkan edit saja file kasir .conf dengan menambahkan
	ServerName 192.168.x.x
	
```
sudo a2ensite 000-default.conf
systemctl reload apache2
systemctl restart apache2
```

LANJUTKAN PERINTAH INI JIKA KONFIGURASI MIKROTIK SUDAH DILAKUKAN DAN SUDAH MENG INSTALL PRESTASHOP
```
rm -rf /var/www/html/install
```