# PRAKTIKUM JARKOM MODUL 2

2. Lakukan konfigurasi routing agar setiap node dapat saling berkomunikasi. Pastikan setiap router dapat mengirimkan paket ke jaringan lain melalui tabel routing yang sesuai. Sertakan bukti bahwa Falcon bisa melakukan ping ke SpiderMan, DoctorStrange, dan ScarletWitch.

---

## IronMan
```bash
# Interfaces
ip addr add 10.142.1.1/24 dev eth1   
ip addr add 10.142.2.1/24 dev eth2   

# Routing
ip route add 10.142.3.0/24 via 10.142.1.2
ip route add 10.142.4.0/24 via 10.142.1.2
ip route add 10.142.5.0/24 via 10.142.2.2
ip route add 10.142.6.0/24 via 10.142.2.2
ip route add 10.142.7.0/24 via 10.142.2.2
```
**Penjelasan:**  
- IronMan jadi **pusat routing**.  
- Interface `eth1` → ke BlackPanther, `eth2` → ke BlackWidow.  
- Semua subnet di belakang BlackPanther diarahkan ke **10.142.1.2**.  
- Semua subnet di belakang BlackWidow diarahkan ke **10.142.2.2**.  

---

##  BlackPanther
```bash
# Interfaces
ip addr add 10.142.1.2/24 dev eth0   
ip addr add 10.142.3.1/24 dev eth1   
ip addr add 10.142.4.1/24 dev eth2   

# Routing
ip route add 10.142.2.0/24 via 10.142.1.1
ip route add 10.142.5.0/24 via 10.142.1.1
ip route add 10.142.6.0/24 via 10.142.1.1
ip route add 10.142.7.0/24 via 10.142.1.1
```
**Penjelasan:**  
- BlackPanther jadi gateway untuk LAN **Falcon** & **CaptainAmerica (10.142.3.0/24)** serta **Hawkeye** & **WinterSoldier (10.142.4.0/24)**.  
- Semua jaringan lain dilempar ke **IronMan (10.142.1.1)**.

---

## BlackWidow
```bash
# Interfaces
ip addr add 10.142.2.2/24 dev eth0   
ip addr add 10.142.6.1/24 dev eth1   
ip addr add 10.142.5.1/24 dev eth2   

# Routing
ip route add 10.142.1.0/24 via 10.142.2.1
ip route add 10.142.3.0/24 via 10.142.2.1
ip route add 10.142.4.0/24 via 10.142.2.1
ip route add 10.142.7.0/24 via 10.142.6.2
```
 **Penjelasan:**  
- BlackWidow jadi gateway untuk LAN **Thor & ScarletWitch (10.142.5.0/24)** dan **Hulk (10.142.6.0/24)**.  
- Jaringan ke SpiderMan & DoctorStrange (10.142.7.0/24) diarahkan ke **Vision (10.142.6.2)**.  

---

## Router Vision
```bash
# Interfaces
ip addr add 10.142.6.2/24 dev eth0   
ip addr add 10.142.7.1/24 dev eth1   

# Routing
ip route add 10.142.1.0/24 via 10.142.6.1
ip route add 10.142.2.0/24 via 10.142.6.1
ip route add 10.142.3.0/24 via 10.142.6.1
ip route add 10.142.4.0/24 via 10.142.6.1
ip route add 10.142.5.0/24 via 10.142.6.1
```
**Penjelasan:**  
- Vision jadi gateway untuk LAN **SpiderMan & DoctorStrange (10.142.7.0/24)**.  
- Semua jaringan lain diarahkan lewat **BlackWidow (10.142.6.1)**.

---

##  Host (Client)

### Falcon
```bash
ip addr add 10.142.3.10/24 dev eth0
ip route add default via 10.142.3.1
```
gateway Falcon adalah **BlackPanther (10.142.3.1)**.

---

### CaptainAmerica
```bash
ip addr add 10.142.3.11/24 dev eth0
ip route add default via 10.142.3.1
```

---

### Hawkeye
```bash
ip addr add 10.142.4.10/24 dev eth0
ip route add default via 10.142.4.1
```

---

### WinterSoldier
```bash
ip addr add 10.142.4.11/24 dev eth0
ip route add default via 10.142.4.1
```

---

### Thor
```bash
ip addr add 10.142.5.10/24 dev eth0
ip route add default via 10.142.5.1
```

---

### ScarletWitch
```bash
ip addr add 10.142.5.11/24 dev eth0
ip route add default via 10.142.5.1
```

---

### Hulk
```bash
ip addr add 10.142.6.10/24 dev eth0
ip route add default via 10.142.6.1
```

---

### SpiderMan
```bash
ip addr add 10.142.7.10/24 dev eth0
ip route add default via 10.142.7.1
```

---

### DoctorStrange
```bash
ip addr add 10.142.7.11/24 dev eth0
ip route add default via 10.142.7.1
```
 3. Lakukan konfigurasi agar semua node dapat terhubung ke internet. Sertakan hasil uji coba dengan melakukan ping ke google.com dari node Falcon, CaptainAmerica, SpiderMan, dan Thor.
---

```bash
# Interfaces
ip addr add 10.142.1.1/24 dev eth1
ip addr add 10.142.2.1/24 dev eth2
ip addr add 192.168.122.100/24 dev eth0   # interface NAT ke internet

# Default route ke NAT
ip route add default via 192.168.122.1

# Aktifkan IP forwarding
echo 1 > /proc/sys/net/ipv4/ip_forward

# NAT rules (semua subnet 10.142.x.x keluar lewat NAT)
iptables -t nat -A POSTROUTING -o eth0 -s 10.142.0.0/16 -j MASQUERADE
```

**Penjelasan**  
- `eth1` → ke BlackPanther (10.142.1.0/24).  
- `eth2` → ke BlackWidow (10.142.2.0/24).  
- `eth0` → NAT internet (192.168.122.x).  
- NAT dipakai agar semua host dengan IP 10.142.x.x bisa akses internet lewat IronMan.

---

## 2. Router Lain Dihubungkan ke IronMan

### BlackPanther
```bash
ip addr add 10.142.1.2/24 dev eth0
ip addr add 10.142.3.1/24 dev eth1
ip addr add 10.142.4.1/24 dev eth2

ip route add default via 10.142.1.1
```
BlackPanther terhubung ke IronMan lewat 10.142.1.1 (gateway).

---

### BlackWidow
```bash
ip addr add 10.142.2.2/24 dev eth0
ip addr add 10.142.6.1/24 dev eth1
ip addr add 10.142.5.1/24 dev eth2

ip route add default via 10.142.2.1
```
BlackWidow terhubung ke IronMan lewat 10.142.2.1 (gateway).

---

### Vision
```bash
ip addr add 10.142.6.2/24 dev eth0
ip addr add 10.142.7.1/24 dev eth1

ip route add default via 10.142.6.1
```
Vision terhubung ke IronMan lewat BlackWidow (10.142.6.1).

---

## 3. Host (Client)

### Falcon
```bash
ip addr add 10.142.3.10/24 dev eth0
ip route add default via 10.142.3.1
```

### CaptainAmerica
```bash
ip addr add 10.142.3.11/24 dev eth0
ip route add default via 10.142.3.1
```

### Thor
```bash
ip addr add 10.142.5.10/24 dev eth0
ip route add default via 10.142.5.1
```

### ScarletWitch
```bash
ip addr add 10.142.5.11/24 dev eth0
ip route add default via 10.142.5.1
```

### SpiderMan
```bash
ip addr add 10.142.7.10/24 dev eth0
ip route add default via 10.142.7.1
```

### DoctorStrange
```bash
ip addr add 10.142.7.11/24 dev eth0
ip route add default via 10.142.7.1
```

### Hulk
```bash
ip addr add 10.142.6.10/24 dev eth0
ip route add default via 10.142.6.1
```

---

## 4. Tambahkan DNS Resolver di Semua Host
```bash
echo "nameserver 8.8.8.8" > /etc/resolv.conf
echo "nameserver 1.1.1.1" >> /etc/resolv.conf
```

---



---

