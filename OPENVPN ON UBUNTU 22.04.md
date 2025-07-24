## <font color="#ffffff">Prasyarat untuk menginstall Open-VPN di Ubuntu 22.04 dan Sejenisnya</font>
1. Vps
2. Akses SSH 
3. Kemahiran CLI
---
## <font color="#ffffff">Practice</font>  
1. Masuk Ke vps anda: gunakan akses SSH untuk masuk ke dalam mesin virtual
```bash
ssh username@your-ip-vps
```
2. Perbarui dan upgrade paket: Pastikan sistem anda up to date 
```shell
sudo apt update && sudo apt upgrade
```
3. Buat pengguna non root untuk debian , jika anda memilih vps ubuntu maka tidak perlu / opsional:
```shell
sudo adduser yourusername sudo usermod -aG sudo yourusername
```
4. Konfigurasikan SSH: untuk meningkatkan keamanan, ubah konfigurasi SSH untuk menontaktifkan login root dan otentikasi kata sandi. Edit file konfigurasi SSH :
```shell
sudo nano /etc/ssh/sshd_config
```
Temukan dan perbarui baris berikut
```bash
PermitRootLogin no PasswordAuthentication no
```
Mulai Ulang SSH agar perubahan berlaku:
```bash
sudo systemctl restart sshd
```
---
## <font color="#ffffff">Menginstall OpenVPN di VPS</font>


1. Update Informasi Packet
```bash
sudo apt update
```
2. Masuk mode root
```shell
sudo su
```
3. Masukkan perintah berikut dan berikan izin menjalankan akses ke sistem :
```shell
curl -O https://raw.githubusercontent.com/angristan/openvpn-install/master/openvpn-install.sh
chmod +x openvpn-install.sh
```
4. Lalu jalankan:
```shell
./openvpn-install.sh
```
Saat OpenVPN sudah terinstall, kamu bisa jalankan kembali, dan kamu dapat memilih untuk:
- Menambah Client
- Menghapus Client
- Uninstall OpenVPN
---
## <font color="#ffffff">Headless Install</font>

kamu bisa **menjalankan skrip `openvpn-install.sh` tanpa harus klik atau jawab pertanyaan satu-satu**. Semua jawaban dikasih lewat **environment variable** (variabel yang diekspor di shell).
Contoh pemakaian sederhana:

```shell
AUTO_INSTALL=y ./openvpn-install.sh

# or

export AUTO_INSTALL=y
./openvpn-install.sh
```

A default set of variables will then be set, by passing the need for user input.

| Variabel                        | Fungsi                                         | Contoh Nilai  |
| ------------------------------- | ---------------------------------------------- | ------------- |
| AUTO INSTALL=y                  | Menjalankan skrip secara otomatis              | y             |
| APPROVE_INSTALL=y               | Setuju dengan install                          | y             |
| APPROVE_IP=y                    | Setuju dengan IP yang dipilih                  | y             |
| IPV6_SUPPORT=n                  | Nonatifkan dukungan IPv6                       | n             |
| PORT_CHOICE=1                   | Pilih port OpenVPN(1=1194)                     | 1             |
| PROTOCOL_CHOICE=1               | Pilih protokol (1=UDP 2= TCP)                  | 1             |
| DNS=1                           | Pilih DNS Internal(1=Cloudflare, 2=Google,dll) | 1             |
| COMPRESSION_ENABLED=n           | Aktifkan Kompresi?(n untuk nonaktif)           | n             |
| CUSTOMIZE_ENC=n                 | Ganti Enkripsi?(n untuk default)               | n             |
| CLIENT=nama-client              | Nama untuk Client                              | user-vpn      |
| PASS=1                          | Client tanpa password(1=no password)           | 1             |
| ENDPOINT=$(curl -4 ifconfig.co) | Untuk Server di balik Nat                      | IP Publik VPS |
### <font color="#ff0000">Catatan</font>
- Jika server kamu dibalik NAT (misal cloud VPS), pakai:
```shell
export ENDPOINT=$(curl -4 ifconfig.co)
```
Ini otomatis akan ambil IP Publik lewat Curl
- Kalau kamu butuh opsi lain (kayak enkripsi spesifik), kamu bisa cek `installQuestions()` dalam skrip-nya untuk tau variabel tambahan.

### <font color="#ff0000">Client Dengan Password Gak Bisa Headless:</font>
Skrip ini tidak mendukung client dengan password saat mode otomatis, karena Easy-RSA butuh input manual.

### <font color="#ff0000">Aman Dipakai Berulang (Idempotent):</font>
Artinya, kamu bisa jalankan skrip berkali-kali dengan parameter yang sama, dan dia:
- Hanya install kalau belum ada
- Tidak overwrite Easy-RSA PKI kalau sudah ada
- Akan regenerasi file client tiap dijalankan

### <font color="#ff0000">Tambah User Secara Otomatis(Headless Add Client):</font>
Kalau OpenVPN sudah terpasang dan kamu mau nambah client baru secara otomatis:

```shell
#!/bin/bash
export MENU_OPTION="1"
export CLIENT="foo"
export PASS="1"
./openvpn-install.sh
```
