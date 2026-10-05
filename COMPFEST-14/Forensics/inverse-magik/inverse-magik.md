# inverse-magik

---

## 📋 **Challenge Description**

---

- **Category : Forensics**
- **Difficulty : Easy**
- **Hint : you might want to find the offset of the corrupted data(s) :u**

back from his pilgrimage, ngab found a note on his desk. "*O' my only apprentice, if thou truly are my apprentice, can you solve this? Ha! may the power of xor and luck be with you!*". with a smirk on his face, ngab quickly starts solving the riddle.

---

## 🔍 Complete Solution

---

**Step 1 : Identifikasi Tipe File garden_blekk**

Diberikan file `eureka.jpg` dan `garden_blekk` , kita coba identifikasi tipe file dari `garden_blekk`

```python
file garden_blekk
```

Output :

```python
garden_blekk: MPEG ADTS, AAC, v4 Main, 64 kHz, stereo + center
```

Berdasarkan output, file `garden_blekk` adalah file bertipe audio, supaya yakin, kita coba cek hex file tersebut.

```python
xxd garden_blekk | head
```

Output :

```python
00000000: fff0 0afc 210a 1a0a 0000 000d 4948 4452  ....!.......IHDR
00000010: 0000 0341 0000 022b 0806 0000 0029 e592  ...A...+.....)..
00000020: 5100 0020 0049 4441 5478 9cec bd59 9325  Q.. .IDATx...Y.%
00000030: c975 e7f7 8f3d e2ee 4bee 9995 5955 5d55  .u...=..K...YU]U
00000040: 5d5d dd5d e8c6 4602 0440 7228 ce8c 08c9  ]].]..F..@r(....
00000050: 28e9 8194 8d4c 661a 7d03 bdf1 5def fa08  (....Lf.}...]...
00000060: 329b 479a 24c2 861c 8024 0870 4491 d87a  2.G.$....$.pD..z
00000070: dfab 6baf caca f5ee f7c6 8d7d 931d cf72  ..k........}...r
00000080: 2878 95d5 5d05 740f 30c8 f36b bb5d 9979  (x..].t.0..k.].y
00000090: e346 78b8 7bb8 9fff 39c7 fd2a 0076 01ac  .Fx.{...9..*.v..
```

Dari hex-nya, ternyata file ini bukanlah file bertipe audio, melainkan file gambar bertipe PNG yang file headernya belum sesuai dengan file header PNG pada umumnya.

Kita perbaiki dulu file headernya agar sesuai dengan file header PNG pada umumnya, dengan mengganti 8 byte pertama dengan byte `89 50 4E 47 0D 0A 1A 0A` .

Setelah diperbaiki, file bisa dibuka, tetapi gambarnya belum sepenuhnya tampil.

![garden_blekk.png](garden_blekk.png)

Berdasarkan hint yang diberikan, kita disuruh untuk mencari offset yang menyebabkan file `garden_blekk.png` corrupt, namun kita belum tau offset mana yang corrupt.

---

**Step 2 : Extract hidden data di file eureka.jpg**

Kita beralih dulu ke file `eureka.jpg` , kita coba buka terlebih dahulu isi dari filenya.

![eureka.jpg](eureka.jpg)

Ternyata tidak ada apa-apa di dalam isi filenya, dan ada kalimat yang sepertinya menyinggung offset dari file `garden_blekk.png` yang corrupt, tapi kita masih belum dikasih tau offset mana yang corrupt.

Kita coba gunakan `binwalk` barangkali ada data yang tersembunyi pada file `eureka.jpg` 

```python
binwalk -e eureka.jpg
```

Output :

```python
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
127940        0x1F3C4         Zip archive data, at least v2.0 to extract, compressed size: 9, uncompressed size: 9, name: eureka.txt

WARNING: One or more files failed to extract: either no utility was found or it's unimplemented
```

Ternyata ada file `eureka.txt` di dalam file tersebut, yang jika kita baca filenya akan menampilkan output.

```python
202 - 205  
```

Dari sini dapat disimpulkan bahwa offset 202 sampai 205 adalah offset yang corrupt dari file `garden_blekk.png` 

---

**Step 3 : XOR offset 202 sampai 205**

Deskripsi chall menyebutkan kata **“XOR”,** jadi mungkin saja offset 202 sampai 205 telah di-XOR, untuk mengubahnya kembali ke nilai asli, kita perlu XOR offset tersebut lagi. Kuncinya tidak mungkin 0 karena XOR dengan 0 tidak akan mengubah apa pun, jadi kunci yang masuk akal adalah meng-XOR setiap byte dengan `0xff` (1111 1111).

```python
with open('garden_blekk.png', 'rb') as f:
    data = bytearray(f.read())

    for i in range(202, 206):
        data[i] ^= 0xff

        with open('fixed.png', 'wb') as d:
            fix = d.write(data)   
```

Di sini saya membuat script python yang meng-XOR offset ke 202 sampai 205 dengan `0xff` , lalu hasil dari XOR akan ditulis kembali ke file baru bernama “fixed.png”.

Setelah itu kita buka file `fixed.png` dan jika kita atur kecerahan dari gambarnya menggunakan aplikasi editing foto, kita akan mendapatkan flag.

![final_flag.png](final_flag.png)

`COMPFEST14{welcome_2_the_mag1k_klubzz}`