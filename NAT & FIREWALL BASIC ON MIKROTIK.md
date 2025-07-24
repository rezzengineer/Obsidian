### Konfigurasi Dasar agar router dapat tersambung ke Internet
1. Lakukan Soft Reset pada mikrotik
2. Masuk ke IP > DHCP client
3. Tambah,lalu klik ether 1 sesuai port ISP berada
4. Masuk ke IP > Firewall > NAT > add new > 
	Chain : srcnat 
	Out.Interface : masukkan ether port ISP
	Action : MASQUERADE
5. Tes apakah router sudah tersambung ke internet dengan masuk pada New Terminal, ketik "Ping 8.8.8.8"
---
### Mengatur router agar dapat memberikan akses internet ke Client / User
1. Masuk ke IP > Addresses > Add new > untuk ip bebas terserah kita asalkan diberi prefix dan port nya diarahkan ke port untuk client. 
2. Masuk ke IP > DHCP Server > SETUP > lalu atur ke ether client.
---
### Mengatur Filter Rules untuk membatasi akses ping, browsing dll
1. Masuk ke IP > Firewall > FIlter Rules 
	Chain : Input (User To Router)
		 Forward (User - Router - Out)
		 Output (Router to User)
	Protocol : tcp (port)
			icmp (ping)
	In.Interface : Ether yang tersambung ke client
	Action : Drop
