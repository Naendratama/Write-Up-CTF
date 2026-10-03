# WPA-ing Out

---

## 🗒️ **Challenge Description**

---

- **Category** : Forensics
- **Difficulty** : Medium
- **Hint 1** : Finding the IEEE 802.11 wireless protocol used in the wireless traffic packet capture is easier with wireshark, the JAWS of the network.
- **Hint 2** : Aircrack-ng can make a pcap file catch big air...and crack a password.

I thought that my password was super-secret, but it turns out that passwords passed over the AIR can be CRACKED, especially if I used the same wireless network password as one in the rockyou.txt credential dump. Use this '[**pcap file**](https://challenge-files.cylabacademy.net/library/14d2e2f2da78ce901cceaff734ed2bca4555097ceb52f486e7836a799b411adc/wpa-ing_out.pcap)' and the rockyou wordlist. The flag should be entered in the academy{XXXXXX} format.

---

## 🔍 Complete Solution

---

**Step 1 : Mengidentifikasi file** 

Diberikan file `wpa-ing_out.pcap` , kita coba identifikasi terlebih dahulu tipe filenya apa.

```bash
file wpa-ing_out.pcap
```

Output :

```bash
wpa-ing_out.pcap: pcap capture file, microsecond ts (little-endian) - version 2.4 (802.11, capture length 65535)
```

Ternyata tipenya adalah pcap capture file.

---

**Step 2 : Membuka dengan tshark**

Kita coba buka dulu isi dari file pcap tersebut menggunakan tshark.

```bash
tshark -r wpa-ing_out.pcap | head
```

Output : 

```bash
    1   0.000000 TPLink_4f:6a:1a → CenturyXinya_17:b0:be 802.11 147 Probe Response, SN=2881, FN=0, Flags=........, BI=100, SSID="Gone_Surfing"
    2   0.000008              → TPLink_4f:6a:1a 802.11 10 Acknowledgement, Flags=........
    3   0.000019 TPLink_4f:6a:1a → CenturyXinya_17:b0:be 802.11 147 Probe Response, SN=2884, FN=0, Flags=........, BI=100, SSID="Gone_Surfing"
    4   0.000022              → TPLink_4f:6a:1a 802.11 10 Acknowledgement, Flags=........
    5   0.099804 TPLink_4f:6a:1a → Broadcast    802.11 142 Beacon frame, SN=2896, FN=0, Flags=........, BI=100, SSID="Gone_Surfing"
    6   3.028862 TPLink_4f:6a:1a → CenturyXinya_17:b0:be 802.11 147 Probe Response, SN=2925, FN=0, Flags=........, BI=100, SSID="Gone_Surfing"
    7   3.028867 TPLink_4f:6a:1a → CenturyXinya_17:b0:be 802.11 147 Probe Response, SN=2926, FN=0, Flags=........, BI=100, SSID="Gone_Surfing"
    8   3.331722 TPLink_4f:6a:1a → CenturyXinya_17:b0:be 802.11 147 Probe Response, SN=2928, FN=0, Flags=........, BI=100, SSID="Gone_Surfing"
    9   3.331726              → TPLink_4f:6a:1a 802.11 10 Acknowledgement, Flags=........
   10   3.432814 TPLink_4f:6a:1a → CenturyXinya_17:b0:be 802.11 147 Probe Response, SN=2929, FN=0, Flags=........, BI=100, SSID="Gone_Surfing"
```

Dari outputnya, sudah kelihatan sekali file pcap tersebut menangkap requests dari WIFI merek TPLink.

Dari deskripsi chall, sudah jelas sekali kita disuruh untuk mendapatkan password yang ada file pcap ini, jadi pasti ada handshake yang terjadi.

---

**Step 3 : Crack Password Wifi**

Di sini kita bisa menggunakan tools bernama `aircrack-ng` untuk mengcrack password wifi yang ada dil file pcap ini dengan wordlists yang sudah disebut di deskripsi chall yaitu rockyou.txt.

```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt wpa-ing_out.pcap
```

- -w : flag untuk file wordlists yang digunakan

Output :

```bash
                               Aircrack-ng 1.7

      [00:00:05] 97000/10303727 keys tested (20604.97 k/s)

      Time left: 8 minutes, 15 seconds                           0.94%

                          KEY FOUND! [ mickeymouse ]

      Master Key     : 70 8A FF 4E 2C 96 E0 0B 51 71 DA 19 D1 9E A7 0B
                       D8 EF FD 21 58 AD 78 EB FA 18 F7 59 3D 57 22 39

      Transient Key  : 7D B2 9C A4 C8 2B AA 63 B3 9A 59 E8 0C 43 66 64
                       D8 42 CC 9F B9 FD 8E 21 DD E9 BC D2 CA 39 03 CF
                       F6 40 3D C8 51 D6 5E F8 D3 B0 58 29 FB FF C6 3F
                       D2 48 ED DE EC 96 87 35 D2 2F 7D 08 6C F3 11 50

      EAPOL HMAC     : 64 B0 20 C5 78 B1 39 FB A3 2A EE B1 E0 F5 82 0D
```

Sudah ketemu passwordnya, dan kita tinggal memasukkan password yang ditemukan ke format flag academy.

`academy{mickeymouse}`