🧱 STEP-BY-STEP: Setup WordPress di Nginx (Ubuntu)
---
1. Persiapan Folder WordPress
```
sudo mkdir -p /var/www/reza.com
cd /tmp
wget https://wordpress.org/latest.zip
unzip latest.zip
sudo mv wordpress/* /var/www/reza.com
```
2. Set hak akses biar Nginx bisa baca
```
sudo chown -R www-data:www-data /var/www/reza.com
sudo chmod -R 755 /var/www/reza.com
```
3. Buat Virtual Host baru
```
sudo nano /etc/nginx/sites-available/reza.com
```
Lalu isi dengan ini:
```nginx
server {
    listen 80;
    server_name www.reza.com;

    root /var/www/reza.com;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
4. Aktifkan virtual host-nya
```
sudo ln -s /etc/nginx/sites-available/reza.com /etc/nginx/sites-enabled/
```
5. Cek konfigurasi
```
sudo nginx -t
```
Kalau muncul syntax is ok dan test is successful, lanjut:
```
sudo systemctl reload nginx
```
6. (Opsional) Matikan default site biar gak ganggu
```
sudo rm /etc/nginx/sites-enabled/default
```
Tapi hati-hati ya! Pastikan site kamu udah jalan dulu sebelum hapus default.
7. Jangan lupa install DB + PHP ext
Kalau belum:
```
sudo apt install php8.3 php8.3-fpm php8.3-mysql
Lalu setup database untuk WordPress:
sudo mysql_secure_installations
sudo mysql
```

```
CREATE DATABASE wp_reza;
CREATE USER 'rezauser'@'localhost' IDENTIFIED BY 'passwordkuaman';
GRANT ALL PRIVILEGES ON wp_reza.* TO 'rezauser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
---
1. Bikin folder baru untuk subdomain
```
sudo mkdir -p /var/www/blog.reza.com
```
(isi folder ini dengan website kedua kamu, misalnya WordPress lagi, static HTML, dsb.)

2. Kasih hak akses
```
sudo chown -R www-data:www-data /var/www/blog.reza.com
sudo chmod -R 755 /var/www/blog.reza.com
```
3. Buat virtual host baru untuk subdomain
```
sudo nano /etc/nginx/sites-available/blog.reza.com
```
Isi:
```nginx
server {
    listen 80;
    server_name blog.reza.com;

    root /var/www/blog.reza.com;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
4. Aktifkan virtual host-nya
```
sudo ln -s /etc/nginx/sites-available/blog.reza.com /etc/nginx/sites-enabled/
```
5. Tes konfigurasi & reload Nginx
```
sudo nginx -t
sudo systemctl reload nginx
```
6. Tambahkan entri hosts (kalau lokal)
Kalau kamu masih tes secara lokal (gak pakai DNS publik), edit /etc/hosts atau di Windows:
192.168.1.144 blog.reza.com