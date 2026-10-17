# JEJAK REPLIKASI

---

#### 📋 **Challenge Description**

- **Category : Forensics**
- **Point : 450**

Insiden janggal terjadi di kantor cabang Surabaya saat jam istirahat makan siang. Seluruh workstation sempat kehilangan koneksi ke domain selama beberapa detik sebelum kembali normal tanpa tanda-tanda kerusakan fisik pada mesin server. Tim tanggap insiden mencabut kabel jaringan dan menyalin rekaman aktivitas domain controller sebelum sistem dimatikan.

---

**Step 1 : Identifikasi Tipe File**

Diberikan file bernama **`ad_evidence.zip`  ,** kita coba cek tipe file tersebut.

```bash
file ad_evidence.zip 
```

Output :

```bash
ad_evidence.zip: Zip archive data, made by v2.0 UNIX, extract using at least v2.0, last modified Oct 11 2026 18:05:44, uncompressed size 1252, method=deflate
```

File tersebut adalah file arsip zip, langsung saja kita extract.

```bash
7z x ad_evidence.zip
```

Setelah kita extract muncul 2 file baru yaitu **`ntds_dump.txt`** dan **`security_audit.log`** 

---

**Step 2 : Identifikasi File ntds_dump.txt**

Kita coba cek isi dari file **`ntds_dump.txt`** 

```bash
[*] Target Domain: CORP.INTERNAL
[*] Dumping Domain Credentials via DRSUAPI (DCSync)...
Administrator:500:aad3b435b51404eeaad3b435b51404ee:444455556666777788889999aaaabbbb:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:55a9b3c4d2e1f0a9b8c7d6e5f4a3b2c1:::
svc_backup:1105:aad3b435b51404eeaad3b435b51404ee:8842af90b1c2d3e4f5a6b7c8d9e0f1a2:::
FLAG_HOLDER:1110:aad3b435b51404eeaad3b435b51404ee:e10adc3949ba59abbe56e057f20f883e::: 
(Description: 
AES_ENCRYPTED_VAULT_HEX:7cbdcda3252ff40a6168f137c6748eed68e678275c814543cb78f52248ccaf4ca3599d2e5fd29975fc4f90d1b145d663; 
IV:ITFEST_IV_16BYTE; 
KEY:Administrator_NTLM_Hash)
```

Terdapat beberapa kredensial beserta beberapa hash yang dipisahkan tanda **`“:”`**, seperti hash pertama itu adalah hash LM, lalu hash kedua itu hash NTLM.

Lalu ada sebuah hex yang sepertinya dienkripsi menggunakan algoritma AES

```bash
7cbdcda3252ff40a6168f137c6748eed68e678275c814543cb78f52248ccaf4ca3599d2e5fd29975fc4f90d1b145d663
```

 dengan KEY nya adalah value hash NTLM dari kredensial Administrator yaitu **`444455556666777788889999aaaabbbb` ,** 

---

**Step 3 : Decode The Hex**

Langsung saja kita decode hex yang terenkripsi tersebut menggunakan KEY dan IV yang sudah kita dapatkan di cyber chef.

![image.png](image.png)

**`ITFEST{dcsync_k3rb3r0s_4d_pwn3d}`**