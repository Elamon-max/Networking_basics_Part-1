# Tarmoq Vazifasi: IP Manzillar va Subnetting Tahlili

## 1-vazifa: IP manzillarni ikkilik shaklga o‘tkazish va subnet maskalarni tanlash

### 1. IP manzillarni ikkilik yozuvga o‘tkazish
IP manzil 4 ta oktetdan iborat bo'lib, har bir oktet 8 bitni (0–255 gacha bo'lgan sonlarni) tashkil qiladi. Ikkilik yozuvni olish uchun har bir oktetni alohida 8 bitli ikkilik songa aylantiramiz.

#### a) IP manzil: `178.101.89.7`

| Oktet | O‘nlik ko'rinishi | Ikkilik ko'rinishi (8 bit) |
| :---: | :---: | :--- |
| **1** | 178 | `10110010` |
| **2** | 101 | `01100101` |
| **3** | 89  | `01011001` |
| **4** | 7   | `00000111` |

* **Natija:** `10110010.01100101.01011001.00000111`

#### b) IP manzil: `201.57.153.161`

| Oktet | O‘nlik ko'rinishi | Ikkilik ko'rinishi (8 bit) |
| :---: | :---: | :--- |
| **1** | 201 | `11001001` |
| **2** | 57  | `00111001` |
| **3** | 153 | `10011001` |
| **4** | 161 | `10100001` |

* **Natija:** `11001001.00111001.10011001.10100001`

---

### 2. Segmentlar uchun subnet maskalarni tanlash
Tarmoq segmentlari soni $N$ ta bo'lib, har bir segment alohida quyi tarmoq (subnet) sifatida ajratiladi.

| Segment nomi | Kerakli hostlar soni | Minimal subnet hajmi ($2^k - 2 \ge \text{hostlar}$) | Subnet maska (CIDR) |
| :--- | :---: | :--- | :---: |
| **Texnologik** | 3 | $2^3 = 8$ host (6 tasi xostlar uchun ishlatiladi) | `/29` |
| **Server** | 16 | $2^5 = 32$ host (30 tasi xostlar uchun ishlatiladi) | `/27` |
| **Foydalanuvchi** | 32 | $2^6 = 64$ host (62 tasi xostlar uchun ishlatiladi) | `/26` |

> 📌 **Eslatma:** Subnet hajmi $2^m$ formulasi bilan hisoblanadi, bunda $m$ – host bitlari sonini bildiradi. Formulada har doim 2 ta manzil (Tarmoq IDsi va Broadcast manzili) xostlarga berilmaydi, shuning uchun real ishlatiladigan xostlar formulasi: **$2^m - 2 \ge \text{kerakli hostlar}$** ko'rinishida bo'ladi.

---

### 📐 Tanlangan maskalarning texnik asoslanishi

#### 1. Texnologik segment (3 ta mashina)
* 3 ta xost uchun minimal quvvat sifatida $2^2$ ni ololmaymiz, chunki $2^2 - 2 = 2$ ta xost bo'ladi va 3 ta mashinaga yetmaydi.
* Keyingi yuqori daraja $2^3 = 8$ ($8 - 2 = 6$ ta xost). Bu 3 ta xost ehtiyojini to'liq qoplaydi va kelajakda kichik kengayishlar uchun zaxira (free space) qoldiradi.
* **Maska:** `/29` ($255.255.255.248$)

#### 2. Server segmenti (16 ta mashina)
* 16 ta xost uchun $/28$ maskasi ($2^4 - 2 = 14$ ta xost) yetarli bo'lmaydi.
* Shuning uchun keyingi qadam $2^5 - 2 = 30$ xost bera oladigan maskani tanlaymiz. Bu 16 ta server uchun yetarli va xavfsiz zaxira hisoblanadi.
* **Maska:** `/27` ($255.255.255.224$)

#### 3. Foydalanuvchi segmenti (32 ta mashina)
* 32 ta xost uchun $/27$ maskasi tanlansa, u jami 30 ta xostga ruxsat beradi ($2^5 - 2 = 30$) va bu 32 ta mashina uchun yetmaydi.
* Shu sababli, bizga $2^6 - 2 = 64$ (62 ta foydalanuvchi sig'adigan) hajmdagi quyi tarmoq kerak bo'ladi.
* **Maska:** `/26` ($255.255.255.192$)

---

### 📊 Yakuniy hisob-kitoblar jadvali

| Segment nomi | Minimal subnet hajmi | Subnet maska (CIDR) | O‘nlik (Decimal) ko‘rinishda |
| :--- | :---: | :---: | :--- |
| **Texnologik** | 8 | `/29` | `255.255.255.248` |
| **Server** | 32 | `/27` | `255.255.255.224` |
| **Foydalanuvchi** | 64 | `/26` | `255.255.255.192` |


# 2-topshiriq: GNS3 muhitini tayyorlash va Cisco 3745 ni import qilish

## 🎯 Topshiriq maqsadi
GNS3 tarmoq simulyatori muhitini o‘rnatish, Cisco 3745 router tasvirini import qilish va loyihani GitHub omboriga (repository) joylashtirish.

---

## 🐧 1. GNS3 ni o‘rnatish (Linux asosida)

### 1.1. GNS3 paketlarini o‘rnatish
Quyidagi buyruqlar orqali rasmiy GNS3 ombori tizimga qo'shiladi va kerakli paketlar o'rnatiladi:

```bash
# Ubuntu/Debian uchun
sudo add-apt-repository ppa:gns3/ppa
sudo apt update
sudo apt install gns3-gui gns3-server -y 
```
.2. ***dumpcap*** uchun ruxsatlarni o‘rnatish
```bash
sudo chmod +x /usr/bin/dumpcap
```
Izoh: dumpcap Wireshark paketini tutish uchun kerak. Bu ruxsat GNS3 da tarmoq interfeyslarini tahlil qilish imkonini beradi.

## 2. Cisco IOS 3745 Tasvirini Import Qilish

### 2.1. Tasvirni Yuklab Olish

Cisco IOS 3745 router tasviri yuklab olindi:

* **Fayl nomi:** `c3745-adventerprisek9-mz.124-25d.bin`

### 2.2. GNS3 Ga Import Qilish

GNS3 ishga tushirildi:

```bash
gns3
```

So'ng quyidagi amallar bajarildi:

1. **Edit → Preferences → QEMU → QEMU VMs** bo‘limiga o'tildi.
2. **New** tugmasi bosildi.
3. **Router** turi tanlandi.
4. **New Image** orqali `c3745-adventerprisek9-mz.124-25d.bin` fayli tanlandi.
5. Router parametrlari tekshirildi.

| Parametr | Qiymat                  |
| -------- | ----------------------- |
| RAM      | 256 MB                  |
| NICs     | 4                       |
| Console  | Telnet (avtomatik port) |

Parametrlar tasdiqlangandan so‘ng **Import** tugmasi bosildi va Cisco 3745 tasviri GNS3 muhitiga muvaffaqiyatli import qilindi.

> **Eslatma:** Cisco 3745 routeri QEMU asosida ishlaydi. Ushbu qurilma VirtualBox yoki VMware texnologiyalaridan foydalanmaydi.

---

## 3. GNS3 Loyihasini Yaratish

### 3.1. Yangi Loyiha

Yangi loyiha yaratildi:

* **Project Name:** `vazifa-2-laboratoriya`
* **Location:** `~/GNS3/projects/`

Yaratish ketma-ketligi:

```text
File → New Project → vazifa-2-laboratoriya
```

Loyiha saqlandi va ish muhiti tayyorlandi.

### 3.2. Router Qo‘shish

Chap paneldagi **Routers** bo‘limidan **Cisco 3745** qurilmasi loyiha maydoniga joylashtirildi.

Router nomi:

```text
R1
```

Router modullari quyidagicha sozlandi:

| Slot   | Modul     |
| ------ | --------- |
| Slot 0 | NM-1FE-TX |
| Slot 1 | NM-4E     |

Sozlash oynasiga quyidagi yo‘l orqali o'tildi:

```text
Right Click → Configure → Slots
```

### 3.3. Konfiguratsiyalarni Saqlash

Router konfiguratsiyasini saqlash uchun quyidagi buyruq bajarildi:

```cisco
R1# copy running-config startup-config
```

Loyihaning o‘zi esa GNS3 orqali saqlandi:

```text
File → Save Project
```

> **Muhim:** Har bir muhim konfiguratsiya o‘zgarishidan so‘ng `startup-config` saqlab borildi. Aks holda router qayta ishga tushirilganda barcha sozlamalar yo‘qolishi mumkin.

![alt text](<screen/Screenshot from 2026-06-08 15-15-00.png>)

# 3-Topshiriq: Multicast So'rovlari (ICMP Orqali)

## Maqsad

Ushbu laboratoriya ishining maqsadi multicast manziliga ICMP Echo Request (ping) so'rovlarini yuborish, Wireshark yordamida tarmoq trafikini tahlil qilish hamda Ethernet (L2), IP (L3) va ICMP qatlamlaridagi maydonlarni o'rganishdan iborat.

---

## 1. Laboratoriya Topologiyasi

### Tarmoq Sxemasi

```text
      [ R1 ]                      [ R2 ]
  10.10.10.1/24              10.10.10.2/24
      |                           |
      +--------(eth0)------------(eth0)--------+
```

### Qurilmalar va Interfeyslar

| Qurilma | Interfeys       | IP manzil  | Subnet maska  |
| ------- | --------------- | ---------- | ------------- |
| R1      | FastEthernet0/0 | 10.10.10.1 | 255.255.255.0 |
| R2      | FastEthernet0/0 | 10.10.10.2 | 255.255.255.0 |

---

## 2. GNS3 Loyihasini Yaratish

### 2.1. Yangi Loyiha

GNS3 muhitida yangi loyiha yaratildi:

```text
File → New Project → multicast-lab → Save
```

### 2.2. Routerlarni Qo'shish

Loyihaga ikkita Cisco 3745 router qo'shildi:

* R1
* R2

Routerlar qo'shilishidan oldin Cisco 3745 tasviri GNS3 muhitida mavjudligi tekshirildi.

### 2.3. Routerlarni Ulanishi

Routerlar FastEthernet interfeyslari orqali o'zaro bog'landi:

```text
R1 FastEthernet0/0 <----> R2 FastEthernet0/0
```

GNS3 ulanishi:

```text
Connections → Ethernet → R1 f0/0 → R2 f0/0
```

---

## 3. Router Konfiguratsiyasi

### 3.1. R1 Konfiguratsiyasi

```cisco
enable
configure terminal
hostname R1

interface fastEthernet 0/0
 ip address 10.10.10.1 255.255.255.0
 no shutdown
exit

end
copy running-config startup-config
```

### 3.2. R2 Konfiguratsiyasi

```cisco
enable
configure terminal
hostname R2

interface fastEthernet 0/0
 ip address 10.10.10.2 255.255.255.0
 no shutdown
exit

end
copy running-config startup-config
```
![alt text](<screen/Screenshot from 2026-06-08 15-42-19.png>)
### 3.3. Ulanishni Tekshirish

R1 routeridan R2 routeriga ping yuborildi:

```cisco
R1# ping 10.10.10.2
```

Kutilayotgan natija:

```text
!!!!!
Success rate is 100 percent (5/5)
```

Bu natija qurilmalar o'rtasidagi bog'lanish muvaffaqiyatli ishlayotganini ko'rsatadi.

---

## 4. Multicast Guruhiga Qo'shilish

Multicast trafikni qabul qilish uchun R2 routeri multicast guruhiga qo'shildi.

### 4.1. IGMP Join

```cisco
configure terminal

interface fastEthernet 0/0
 ip igmp join-group 224.0.0.9

end
copy running-config startup-config
```

Multicast manzil:

```text
224.0.0.9
```

### 4.2. IGMP Holatini Tekshirish

```cisco
R2# show ip igmp groups
```

Natijada `224.0.0.9` multicast guruhi ro'yxatda ko'rinishi kerak.

---

## 5. Wireshark Capture va Multicast Ping

### 5.1. Capture Boshlash

GNS3 da routerlar orasidagi ulanish ustiga o'ng tugma bosilib quyidagi amal bajarildi:

```text
Start Capture
```

Wireshark dasturi ishga tushirildi va trafik yozib olinishi boshlandi.

### 5.2. Multicast Ping Yuborish

R1 routeridan multicast manziliga ping yuborildi:

```cisco
R1# ping 224.0.0.9 repeat 5
```

yoki

```cisco
R1# ping 224.0.0.9
```
![alt text](<screen/Screenshot from 2026-06-08 15-54-36.png>)
### 5.3. Capture Faylini Saqlash

Capture yakunlangandan so'ng:

```text
File → Save As → multicast.pcap
```

fayli loyiha papkasiga saqlandi.

---

## 6. Wireshark Paket Tahlili

### Filtr

Multicast ICMP paketlarini aniqlash uchun quyidagi filtr qo'llanildi:

```wireshark
icmp and ip.dst == 224.0.0.9
```

### Paket Maydonlari

| Qatlam   | Maydon          | Qiymat            | Izoh                          |
| -------- | --------------- | ----------------- | ----------------------------- |
| Ethernet | Destination MAC | 01:00:5E:00:00:09 | Multicast MAC manzili         |
| Ethernet | Source MAC      | Router MAC        | R1 qurilmasining MAC manzili  |
| IP       | Source IP       | 10.10.10.1        | R1 manzili                    |
| IP       | Destination IP  | 224.0.0.9         | Multicast guruh manzili       |
| IP       | TTL             | 1                 | Mahalliy multicast trafik     |
| ICMP     | Type            | 8                 | Echo Request                  |
| ICMP     | Code            | 0                 | Standart qiymat               |
| ICMP     | Checksum        | Dinamik           | Paket yaxlitligini tekshirish |

---

## 7. Asosiy Savolga Javob

### Savol

ICMP multicast so'rovi qaysi MAC manziliga yuboriladi?

### Javob

Multicast ICMP so'rovi quyidagi multicast MAC manziliga yuboriladi:

```text
01:00:5E:00:00:09
```

### Izoh

* IP multicast manzili: `224.0.0.9`
* Mos multicast MAC manzili: `01:00:5E:00:00:09`
* `01:00:5E` prefiksi multicast trafik uchun rezervlangan.
* Paket broadcast manziliga emas, multicast MAC manziliga yuboriladi.

---

## 8. Yakuniy Natijalar

### multicast Fayli

```text
ICMP multicast so'rovi quyidagi MAC manziliga yuboriladi:

01:00:5E:00:00:09

Izoh:
- Bu 224.0.0.9 multicast IP manziliga mos multicast MAC manzildir.
- Birinchi 24 bit multicast uchun rezervlangan.
- Qolgan bitlar multicast IP manzilidan hosil qilinadi.
- Bu broadcast MAC manzili emas.
```

### multicast.pcap Fayli

Capture faylida quyidagi trafiklar qayd etildi:

* ICMP Echo Request (R1 → 224.0.0.9)
* ICMP Echo Reply (R2 → 10.10.10.1)

---


## 10. Qo'shimcha Tahlil

### Broadcast va Multicast Taqqoslanishi

| Tur            | Manzil            | Qabul qiluvchilar              |
| -------------- | ----------------- | ------------------------------ |
| Broadcast (L2) | FF:FF:FF:FF:FF:FF | Barcha qurilmalar              |
| Broadcast (L3) | 255.255.255.255   | Barcha hostlar                 |
| Multicast (L2) | 01:00:5E:00:00:09 | Guruh a'zolari                 |
| Multicast (L3) | 224.0.0.9         | IGMP orqali qo'shilgan hostlar |


## Xulosa

Laboratoriya davomida multicast guruhiga ICMP so'rovlari yuborildi va Wireshark yordamida paketlar tahlil qilindi. Tahlil natijasida multicast IP manzili `224.0.0.9` uchun mos multicast MAC manzili `01:00:5E:00:00:09` ekanligi aniqlandi. Multicast trafik muvaffaqiyatli ishlashi uchun qabul qiluvchi qurilma IGMP yordamida multicast guruhiga a'zo bo'lishi zarurligi kuzatildi.


# 4-Topshiriq: ARP (Address Resolution Protocol)

## Maqsad

Ushbu laboratoriya ishining maqsadi ARP protokolining ishlash tamoyillarini o‘rganish, Wireshark yordamida ARP va ICMP paketlarini tahlil qilish, ARP jadvalini xavfsizlik nuqtai nazaridan baholash hamda ARP spoofing (soxtalashtirish) mantig‘ini tushunishdan iborat.

---

## 1. Laboratoriya Topologiyasi

### Tarmoq Sxemasi

```text
      [ R1 ]                      [ R2 ]
  10.10.10.1/24              10.10.10.2/24
      |                           |
      +--------(eth0)------------(eth0)--------+
```

### Qurilmalar va Interfeyslar

| Qurilma | Interfeys       | IP manzil  | MAC manzili (misol) |
| ------- | --------------- | ---------- | ------------------- |
| R1      | FastEthernet0/0 | 10.10.10.1 | 00:50:79:66:68:00   |
| R2      | FastEthernet0/0 | 10.10.10.2 | 00:50:79:66:68:01   |

---

## 2. ARP Jadvalini O‘rganish

### 2.1. Pingdan Oldin ARP Jadvali

R1 routerida ARP jadvali tekshirildi:

```cisco
R1# show arp
```

Kutilayotgan natija:

* 10.10.10.2 uchun yozuv mavjud emas.
* Yoki mavjud yozuv eskirgan bo‘lishi mumkin.

### 2.2. Ping Yuborish

```cisco
R1# ping 10.10.10.2 repeat 1
```

### 2.3. Pingdan Keyin ARP Jadvali

```cisco
R1# show arp
```

Namuna natija:

```text
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.10.10.2       0          0050.7966.6801  ARPA   FastEthernet0/0
Internet  10.10.10.1       -          0050.7966.6800  ARPA   FastEthernet0/0
```

ARP so‘rovi natijasida R1 qurilmasi 10.10.10.2 IP manziliga mos MAC manzilini o‘rgandi va ARP jadvaliga qo‘shdi.

---

## 3. Wiresharkda ARP Paketlarini Tahlil Qilish

### 3.1. Capture Filtri

Wiresharkda quyidagi filtr qo‘llanildi:

```wireshark
arp or icmp
```

### 3.2. ARP Request Paketi

Birinchi ARP so‘rovi quyidagi maydonlarga ega bo‘ldi:

| Qatlam   | Maydon          | Qiymat            | Izoh              |
| -------- | --------------- | ----------------- | ----------------- |
| Ethernet | Destination MAC | FF:FF:FF:FF:FF:FF | Broadcast manzili |
| Ethernet | Source MAC      | 00:50:79:66:68:00 | R1 MAC manzili    |
| ARP      | Hardware Type   | Ethernet (1)      | Ethernet tarmog‘i |
| ARP      | Protocol Type   | IPv4 (0x0800)     | IPv4 protokoli    |
| ARP      | Operation       | Request (1)       | So‘rov            |
| ARP      | Sender MAC      | 00:50:79:66:68:00 | R1 MAC            |
| ARP      | Sender IP       | 10.10.10.1        | R1 IP             |
| ARP      | Target MAC      | 00:00:00:00:00:00 | Noma'lum          |
| ARP      | Target IP       | 10.10.10.2        | Qidirilayotgan IP |

### 3.3. ARP Reply Paketi

ARP javob paketi quyidagicha ko‘rindi:

| Qatlam   | Maydon          | Qiymat            | Izoh   |
| -------- | --------------- | ----------------- | ------ |
| Ethernet | Destination MAC | 00:50:79:66:68:00 | R1 MAC |
| Ethernet | Source MAC      | 00:50:79:66:68:01 | R2 MAC |
| ARP      | Operation       | Reply (2)         | Javob  |
| ARP      | Sender MAC      | 00:50:79:66:68:01 | R2 MAC |
| ARP      | Sender IP       | 10.10.10.2        | R2 IP  |
| ARP      | Target MAC      | 00:50:79:66:68:00 | R1 MAC |
| ARP      | Target IP       | 10.10.10.1        | R1 IP  |

---

## 4. Asosiy Savolga Javob

### Savol

Birinchi ARP paketida so‘rov qaysi MAC manziliga yuboriladi va bu manzil nima deb ataladi?

### Javob

```text
FF:FF:FF:FF:FF:FF
```

Bu manzil **Broadcast MAC Address** (eshittirish MAC manzili) deb ataladi.

### Izoh

ARP so‘rovi tarmoqdagi barcha qurilmalarga yuboriladi va quyidagi mazmunga ega bo‘ladi:

> "Kimda 10.10.10.2 IP manzili bor?"

So‘rovni barcha qurilmalar qabul qiladi, ammo faqat kerakli IP manzilga ega qurilma javob qaytaradi.
![alt text](<screen/Screenshot from 2026-06-08 16-12-28.png>)
---

## 5. ARP Jadvalini Xavfsizlik Nuqtai Nazaridan Tahlil Qilish

### Namuna ARP Jadvali

```text
Protocol  Address          Age (min)  Hardware Addr   Type      Interface
Internet  10.10.10.1       -          0050.7966.6800  STATIC    FastEthernet0/0
Internet  10.10.10.2       0          0050.7966.6801  DYNAMIC   FastEthernet0/0
Internet  192.168.1.1      5          0001.2345.6789  DYNAMIC   FastEthernet0/1
```

### Ustunlar Tahlili

| Ustun         | Tavsifi             |
| ------------- | ------------------- |
| Protocol      | L3 protokol turi    |
| Address       | IP manzil           |
| Age (min)     | Yozuv yoshi         |
| Hardware Addr | MAC manzil          |
| Type          | STATIC yoki DYNAMIC |
| Interface     | Interfeys nomi      |

### STATIC va DYNAMIC Taqqoslash

| Turi    | Afzalligi                  | Xavfi                          |
| ------- | -------------------------- | ------------------------------ |
| DYNAMIC | Avtomatik o‘rganiladi      | ARP spoofing hujumlariga ochiq |
| STATIC  | Soxtalashtirishga chidamli | Qo‘lda boshqariladi            |

### Asosiy Xavfsizlik Xatarlari

* Man-in-the-Middle (MITM)
* DoS (Denial of Service)
* Trafikni kuzatish va tinglash
* ARP jadvalini noto‘g‘ri yozuvlar bilan zaharlash

---

## 6. ARP Spoofing Mantig‘i

### ARP Paket Tuzilishi

```text
ARP Header
│
├── Hardware Type
├── Protocol Type
├── Hardware Size
├── Protocol Size
├── Operation
├── Sender MAC
├── Sender IP
├── Target MAC
└── Target IP
```

### MITM Uchun Soxtalashtiriladigan Maydonlar

| Maydon     | Qiymat               |
| ---------- | -------------------- |
| Sender MAC | Hujumchi MAC manzili |
| Sender IP  | Gateway IP manzili   |
| Target MAC | Qurbon MAC manzili   |
| Operation  | Reply (2)            |

### MITM Hujumining Ishlash Tartibi

1. Hujumchi qurbon va gateway MAC/IP manzillarini aniqlaydi.
2. Qurbon qurilmaga soxta ARP javobi yuboradi.
3. Gateway qurilmaga ham soxta ARP javobi yuboradi.
4. Ikkala qurilma trafikni hujumchi orqali yubora boshlaydi.
5. Hujumchi trafikni kuzatishi yoki o‘zgartirishi mumkin.

### Himoya Usullari

* Static ARP yozuvlari
* Dynamic ARP Inspection (DAI)
* Port Security
* VLAN segmentatsiyasi
* Private VLAN

---

## 7. Gratuitous ARP

### Ta’rif

Gratuitous ARP — bu hech qanday ARP so‘rovisiz yuboriladigan ARP javobidir.

### Qo‘llanilish Holatlari

* IP manzil takrorlanishini aniqlash
* Failover jarayonlari
* MAC manzili o‘zgarishini e'lon qilish
* ARP jadvallarini yangilash

### Wireshark Filtri

```wireshark
arp.duplicate-address-detected
```

---

## 8. Yakuniy Javoblar

### 1-Savol

```text
Birinchi ARP so'rovi FF:FF:FF:FF:FF:FF manziliga yuboriladi.
Bu broadcast MAC manzili deb ataladi.
```

### 2-Savol

ARP spoofing hujumida quyidagi maydonlar soxtalashtiriladi:

* Sender MAC
* Sender IP
* Target MAC
* Operation (Reply)

Natijada MITM hujumi amalga oshirilishi mumkin.

### 3-Savol

Dinamik ARP yozuvlari ARP spoofing hujumlariga nisbatan zaif hisoblanadi. Shu sababli muhim qurilmalar uchun statik ARP yozuvlaridan foydalanish tavsiya etiladi.

---


## Xulosa

Laboratoriya davomida ARP protokolining ishlashi amaliy ravishda o‘rganildi. Tahlil natijasida ARP so‘rovlari `FF:FF:FF:FF:FF:FF` broadcast MAC manziliga yuborilishi, ARP jadvali esa IP va MAC manzillar o‘rtasidagi moslikni saqlashi aniqlandi. Shuningdek, dinamik ARP yozuvlari ARP spoofing hujumlariga moyil bo‘lishi va MITM hujumlari uchun asosiy vektorlardan biri ekanligi ko‘rib chiqildi.
