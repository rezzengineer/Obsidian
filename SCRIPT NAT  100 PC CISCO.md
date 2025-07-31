
- **Router0:**
    
    - Fa0/0 = IP Publik (misal ke luar/internet)
        
    - Fa1/0 = IP DNS/public DNS (anggap seperti ISP internal)
        
    - Fungsinya cuma pas-pasan: nembus ke luar + DNS
        
- **Router1:**
    
    - DHCP server buat client (misal LAN)
        
    - NAT Overload biar LAN bisa akses luar
        
    - Terhubung ke Router0 via salah satu interface
        

---

## 🧠 **Topologi Singkat**

```
[PC LAN] -- [SW] -- Fa0/0 [Router1] Fa1/0 -- Fa0/0 [Router0] Fa1/0 -- [DNS Server/public IP]
```

---

## 🛠️ **Konfigurasi Script Router0**

```bash
hostname Router0
!
interface FastEthernet0/0
 ip address 10.10.0.1 255.255.255.0   ! IP Publik simulasi
 no shutdown
!
interface FastEthernet1/0
 ip address 8.8.8.8 255.255.255.0       ! IP DNS atau ISP internal
 no shutdown
!
exit
```

---

## 🛠️ **Konfigurasi Script Router1 (DHCP + NAT)**

```bash
hostname Router1
!
interface FastEthernet1/0
 ip address 192.168.10.1 255.255.255.0   ! LAN
 ip nat inside
 no shutdown
!
interface FastEthernet0/0
 ip address 10.10.0.2 255.255.255.0       ! Menuju Router0
 ip nat outside
 no shutdown
!
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp pool LANPOOL
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
!
access-list 1 permit 192.168.10.0 0.0.0.255
ip nat inside source list 1 interface FastEthernet1/0 overload
!
ip route 0.0.0.0 0.0.0.0 10.10.0.1        ! Default route ke Router0
```

---

### 🧪 IP Device Setup:

| Perangkat     | IP        | Gateway                     | DNS     |
| ------------- | --------- | --------------------------- | ------- |
| PC LAN        | DHCP      | 192.168.10.1                | 8.8.8.8 |
| Router0 Fa0/0 | 10.10.0.1 | -                           | -       |
| Router0 Fa1/0 | 8.8.8.8   | -                           | -       |
| Router1 Fa1/0 | 10.0.0.2  | ke 10.0.0.1 (Router0 Fa0/0) |         |

---
