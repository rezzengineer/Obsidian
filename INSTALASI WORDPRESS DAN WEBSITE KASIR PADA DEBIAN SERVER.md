## **Pendahuluan**

Banyak orang mengandalkan control panel seperti cPanel atau aaPanel untuk mengelola server. Padahal, kalau kita ngerti caranya, semuanya bisa dilakukan langsung lewat CLI (Command Line Interface) — lebih ringan, lebih aman, dan tanpa bloatware.  
Artikel ini bakal ngebahas step-by-step instalasi:

- **Web server:** Nginx
    
- **Database:** MySQL 8.0
    
- **PHP:** Versi 8.3
    
- **Manajemen database:** phpMyAdmin (untuk impor database website kasir)
    
- **Platform:** Debian Server (minimal install)
    

Dengan hasil akhir:

1. **WordPress** — sebagai CMS utama.
    
2. **Website Kasir berbasis PHP & MySQL** — di folder berbeda.
    

---

## **1. Persiapan Awal**

Login ke server Debian kalian via SSH:

```bash
ssh user@IP_SERVER
```
![[Pasted image 20250810200556.png]]
Update semua paket:
```bash
sudo apt update && sudo apt upgrade -y
```
![[Pasted image 20250810191906.png]]
Install tools dasar:

```bash
sudo apt install unzip wget curl -y
```
![[Pasted image 20250810192123.png]]
---

## **2. Install Nginx**

```bash
sudo apt install nginx -y
```
![[Pasted image 20250810192152.png]]
Aktifkan Nginx agar jalan otomatis saat boot:

```bash
sudo systemctl enable nginx
sudo systemctl start nginx
```
![[Pasted image 20250810192221.png]]
Cek status:
```bash
sudo systemctl status nginx
```
![[Pasted image 20250810192241.png]]
---

## **3. Install Mysql **

### Install MysqlServer
Sebelum install Mysql server, kita tambahkan dulu repository Mysql Versi 8.0
```shell
sudo apt install gnupg2
sudo wget https://dev.mysql.com/get/mysql-apt-config_0.8.29-1_all.deb
sudo dpkg -i mysql-apt-config_0.8.29-1_all.deb
sudo apt update
```
```shell
sudo apt install mysql-server -y
```
![[Pasted image 20250810192613.png]]
Masukkan password untuk mengamankan akun root database anda
### Amankan instalasi MySQL

```bash
sudo mysql_secure_installation
```

Jawaban umum:

- Set root password: **Y** (tetapi jika sudah mengatur diawal maka tidak perlu)
    
- Remove anonymous users: **Y**
    
- Disallow root remote login: **Y**
    
- Remove test database: **Y**
    
- Reload privilege tables: **Y**
    

---

## **4. Install PHP 8.3 + Modul**

Debian biasanya default PHP lama, jadi tambahkan repo **Sury**:

```bash
sudo apt install ca-certificates apt-transport-https software-properties-common -y
sudo wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/php.list
sudo apt update
```

Install PHP 8.3 dan modul untuk WordPress & website kasir:

```bash
sudo apt install php8.3 php8.3-fpm php8.3-mysql php8.3-xml php8.3-curl php8.3-zip php8.3-mbstring php8.3-gd -y
```

Aktifkan PHP-FPM:

```bash
sudo systemctl enable php8.3-fpm
sudo systemctl start php8.3-fpm
```

---

## **5. Install phpMyAdmin**

```bash
sudo apt install phpmyadmin -y
```

- Pilih **nginx** tetapi jika tidak ada di pilihan → pilih **None**.
    
- Nanti kita konfigurasi manual di Nginx.
    

Buat symlink phpMyAdmin:

```bash
sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
```
lalu kasih permission :
```sh
sudo chown -R www-data:www-data /usr/share/phpmyadmin
sudo chmod -R 755 /usr/share/phpmyadmin
```
Kemudian buka :
```sh
sudo nano /etc/nginx/sites-available/default
```
Tambahkan pada kolom server {} :

```nginx
location /phpmyadmin {
    root /usr/share/;
    index index.php index.html index.htm;
    location ~ ^/phpmyadmin/(.+\.php)$ {
        try_files $uri =404;
        root /usr/share/;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
    location ~* ^/phpmyadmin/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ {
        root /usr/share/;
    }
}
```
---

## **6. Konfigurasi Virtual Host Nginx**

Misal kalian mau dua website:

- WordPress → `wordpress.local`
    
- Website kasir → `kasir.local`
    

Buat folder:

```bash
sudo mkdir -p /var/www/wordpress1
sudo mkdir -p /var/www/kasir
```

Set ownership:

```bash
sudo chown -R www-data:www-data /var/www/wordpress1 /var/www/kasir
sudo chmod -R 755 /var/www
```

Buat konfigurasi Nginx untuk WordPress:

```bash
sudo nano /etc/nginx/sites-available/wordpress1
```

Isi:

```nginx
server {
    listen 80;
    server_name reza.local;
    root /var/www/wordpress1;

    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Untuk Website Kasir:

```bash
sudo nano /etc/nginx/sites-available/kasir
```

Isi:

```nginx
server {
    listen 80;
    server_name kasir.local;
    root /var/www/kasir;

    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

Aktifkan site:

```bash
sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/kasir /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## **7. Install WordPress**

Masuk folder WordPress:

```bash
cd /var/www/wordpress
sudo wget https://wordpress.org/latest.zip
sudo unzip latest.zip
sudo mv wordpress/* .
sudo rm -rf wordpress latest.zip
sudo chown -R www-data:www-data /var/www/wordpress
```

Buat database WordPress:

```bash
sudo mysql -u root -p
```

```sql
CREATE DATABASE wp_db;
CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'passwordku';
GRANT ALL PRIVILEGES ON wp_db.* TO 'wp_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
![[Pasted image 20250810193954.png]]
---

## **8. Install Website Kasir**

Pastikan kita sudah memiliki file website kasir, kita akan melakukan SFTP dari host (windows) menuju Server :

```
#saya menggunakan SFTP dan FileZilla, jika anda ingin menggunakan FTP server dari server pun silahkan.
```
```shell
#Sebelum ke FileZilla, pastikan anda berikan permission supaya kita bisa upload file dari host (Windows)
sudo usermod -aG www-data reza
sudo chmod -R 775 /var/www/kasir
```
![[Pasted image 20250810194316.png]]
Untuk perbedaannya adalah : 
- SFTP menggunakan port 22, jadi menggunakan layanan ssh, sedangkan FTP menggunakan port 21
![[Pasted image 20250810194445.png]]
Masuk ke lokasi folder website kasir kita, kemudian upload file website kasir kita:
![[Pasted image 20250810195318.png]]
Kemudian kita cek apakah file benar benar ter upload:
```bash
cd /var/www/kasir
ls
```
![[Pasted image 20250810195422.png]]
Jika sudah muncul, kita lanjut untuk ekstract file nya:
```shell
 sudo unzip 10.80.0.99_20250801_064528.zip
```
Buat database kasir:

```bash
sudo mysql -u root -p
```

```sql
CREATE DATABASE kasir_db;
CREATE USER 'kasir_user'@'localhost' IDENTIFIED BY 'passwordkasir';
GRANT ALL PRIVILEGES ON kasir_db.* TO 'kasir_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
![[Pasted image 20250810201055.png]]
Import database kasir via phpMyAdmin:

1. Akses `http://IP_SERVER/phpmyadmin`
    
2. Login sebagai `root` 
    
3. Pilih database `db_toko` → Import → Choose File `.sql` → Jalankan.
    ![[Pasted image 20250810201745.png]]
	![[Pasted image 20250810201815.png]]
	![[Pasted image 20250810201848.png]]
	![[Pasted image 20250810201909.png]]
	![[Pasted image 20250810201921.png]]
	Lalu Scroll kebawah dan pencet :
	![[Pasted image 20250810201959.png]]
---

## **9. Uji Coba**

- WordPress → `http://wordpress.local`
    
- Website Kasir → `http://kasir.local`
    
- phpMyAdmin → `http://IP_SERVER/phpmyadmin`
    

Kalau semua lancar, lo udah punya 2 website berjalan di satu server Debian full CLI.

---

## **Penutup**

Mengelola server full CLI emang keliatan ribet di awal, tapi lo bakal dapat:

- Performa lebih optimal tanpa panel
    
- Pemahaman mendalam tentang Linux, Nginx, PHP, dan MySQL
    
- Kontrol penuh atas keamanan dan konfigurasi
    

Dengan tutorial ini, lo bisa deploy CMS seperti WordPress **dan** aplikasi custom seperti website kasir dalam satu server tanpa nambahin beban dari control panel.

---

Kalau mau, gue bisa bikin **versi markdown siap publish** yang udah ada syntax highlighting warna-warni buat blog lo biar pembaca makin betah bacanya.  
Mau gue bikin juga?