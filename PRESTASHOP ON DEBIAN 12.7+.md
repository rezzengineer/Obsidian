```
su -
#Masukkan repository
nano /etc/apt/sources.list/ 
apt update
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
mkdir -p /var/www/html/kasir
cd /var/www/html/kasir/
wget https://github.com/PrestaShop/PrestaShop/releases/download/8.2.2/prestashop_8.2.2.zip
unzip prestashop(pencet tab aja)
unzip prestashop.zip
chown -R www-data:www-data /var/www/html/kasir/
```

```
cd /etc/apache2/sites-available/
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/kasir.conf
nano kasir.conf
```
Silahkan edit saja file kasir .conf dengan menambahkan
	ServerName 192.168.x.x
	DocumentRoot /var/www/html/kasir
	
```
sudo a2ensite kasir.conf
systemctl reload apache2
systemctl restart apache2
```

LANJUTKAN PERINTAH INI JIKA KONFIGURASI MIKROTIK SUDAH DILAKUKAN DAN SUDAH MENG INSTALL PRESTASHOP
```
rm -rf /var/www/html/kasir/install
```