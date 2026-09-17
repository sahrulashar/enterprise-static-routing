# 🌐 Small Enterprise Network — Static Routing

Simulasi jaringan enterprise skala kecil menggunakan **Cisco Packet Tracer** dengan implementasi **Static Routing** antar tiga lokasi: Headquarters, Branch 1, dan Branch 2.

---

## 📋 Deskripsi Proyek

Proyek ini mensimulasikan topologi jaringan enterprise yang terdiri dari tiga router yang saling terhubung melalui link WAN point-to-point. Setiap lokasi memiliki jaringan LAN tersendiri, dan komunikasi antar lokasi dikonfigurasi menggunakan static routing.

---

## 🗺️ Topologi Jaringan

```
HeadQuarters (192.168.10.0/24)
        |
       R1 (Gig0/0/0: 10.0.0.1)
        |
   10.0.0.0/30
        |
       Internet/R2 (Gig0/0/0: 10.0.0.2 | Gig0/1/0: 20.0.0.2 | Gig0/2/0: 30.0.0.1)
        |              \
  20.0.0.0/30      30.0.0.0/30
        |                \
       R2-Branch1         R3-Branch2
(192.168.20.0/24)    (192.168.30.0/24)
```

---

## 🖥️ Perangkat & Alamat IP

### Router

| Router | Interface     | IP Address    | Terhubung ke              |
|--------|---------------|---------------|---------------------------|
| R1     | Gig0/0/0      | 10.0.0.1/30   | Internet Router (R-Internet) |
| R1     | Gig0/1        | 192.168.10.1/24 | SW1 (HQ LAN)            |
| Internet | Gig0/0/0   | 10.0.0.2/30   | R1                        |
| Internet | Gig0/1/0   | 20.0.0.2/30   | R2 (Branch 1)             |
| Internet | Gig0/2/0   | 30.0.0.1/30   | R3 (Branch 2)             |
| R2     | Gig0/0/0      | 20.0.0.1/30   | Internet Router           |
| R2     | Gig0/0        | 192.168.20.1/24 | SW2 (Branch 1 LAN)      |
| R3     | Gig0/0/0      | 30.0.0.2/30   | Internet Router           |
| R3     | Gig0/0        | 192.168.30.1/24 | SW3 (Branch 2 LAN)      |

### End Devices

| Device | Lokasi        | IP Address      | Gateway         |
|--------|---------------|-----------------|-----------------|
| PC1    | HeadQuarters  | 192.168.10.10/24 | 192.168.10.1   |
| PC2    | HeadQuarters  | 192.168.10.11/24 | 192.168.10.1   |
| PC3    | Branch 1      | 192.168.20.10/24 | 192.168.20.1   |
| PC4    | Branch 1      | 192.168.20.11/24 | 192.168.20.1   |
| PC5    | Branch 2      | 192.168.30.10/24 | 192.168.30.1   |
| PC6    | Branch 2      | 192.168.30.11/24 | 192.168.30.1   |

---

## 🔗 Link WAN (Point-to-Point)

| Segmen           | Network        | R1 Side   | R2 Side   |
|------------------|----------------|-----------|-----------|
| HQ ↔ Internet    | 10.0.0.0/30    | 10.0.0.1  | 10.0.0.2  |
| Branch1 ↔ Internet | 20.0.0.0/30  | 20.0.0.1  | 20.0.0.2  |
| Branch2 ↔ Internet | 30.0.0.0/30  | 30.0.0.2  | 30.0.0.1  |

---

## ⚙️ Konfigurasi Static Route

### R1 (HeadQuarters)
```
ip route 192.168.20.0 255.255.255.0 10.0.0.2
ip route 192.168.30.0 255.255.255.0 10.0.0.2
```

### R2 (Branch 1)
```
ip route 192.168.10.0 255.255.255.0 20.0.0.2
ip route 192.168.30.0 255.255.255.0 20.0.0.2
```

### R3 (Branch 2)
```
ip route 192.168.10.0 255.255.255.0 30.0.0.1
ip route 192.168.20.0 255.255.255.0 30.0.0.1
```

---

## ✅ Verifikasi & Pengujian

### Routing Table

**R1** — Mengenal jaringan Branch 1 dan Branch 2 via 10.0.0.2
```
C    10.0.0.0/30       - GigabitEthernet0/0/0
C    192.168.10.0/24   - GigabitEthernet0/1
S    192.168.20.0/24 [1/0] via 10.0.0.2
S    192.168.30.0/24 [1/0] via 10.0.0.2
```

**R2** — Mengenal jaringan HQ dan Branch 2 via 20.0.0.2
```
C    20.0.0.0/30       - GigabitEthernet0/0/0
C    192.168.20.0/24   - GigabitEthernet0/0
S    192.168.10.0/24 [1/0] via 20.0.0.2
S    192.168.30.0/24 [1/0] via 20.0.0.2
```

**R3** — Mengenal jaringan HQ dan Branch 1 via 30.0.0.1
```
C    30.0.0.0/30       - GigabitEthernet0/0/0
C    192.168.30.0/24   - GigabitEthernet0/0
S    192.168.10.0/24 [1/0] via 30.0.0.1
S    192.168.20.0/24 [1/0] via 30.0.0.1
```

### Ping Test (dari PC1 - HeadQuarters)

```
C:\>ping 192.168.20.10   → Reply 4/4, TTL=125 ✅
C:\>ping 192.168.30.11   → Reply 4/4, TTL=125 ✅
```

### Traceroute (dari PC1 ke 192.168.30.10)

```
Tracing route to 192.168.30.10 over a maximum of 30 hops:

  1   0 ms    192.168.10.1   (R1 - Gateway HQ)
  2   0 ms    10.0.0.2       (Internet Router)
  3   0 ms    30.0.0.2       (R3 - Branch 2)
  4   0 ms    192.168.30.10  (PC5 - Destination)

Trace complete.
```

---

## 🛠️ Tools yang Digunakan

- **Cisco Packet Tracer** — Simulasi jaringan
- **Cisco IOS** — Konfigurasi router

---

## 📁 File

| File | Deskripsi |
|------|-----------|
| `topology.pkt` | File simulasi Cisco Packet Tracer |

---

## 📚 Konsep yang Dipelajari

- Subnetting dan pengalokasian IP address
- Konfigurasi interface router (Gigabit Ethernet)
- Static Routing antar multiple network
- Verifikasi routing menggunakan `show ip route`
- Pengujian konektivitas dengan `ping` dan `tracert`
- Arsitektur jaringan Hub-and-Spoke (Internet router sebagai hub)

---

## 👤 Author

**Sahrul Ashar**  
IT Infrastructure & Network Enthusiast  
Universitas Lamappapoleonro — Teknik Informatika
