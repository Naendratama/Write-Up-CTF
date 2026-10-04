# correction

---

#### **📋 Challenge Description**

---

- **Category : Forensics**
- **Difficulty : Medium**
- **Hint 1 : what can be represented as black and white picture i wonder…**
- **Hint 2 : usually, checksum helps to verify data integrity.**
- **Tags : PNG, CRC, Binary Image Processing**
    
    Ngab is an adolescent boy well-known for his ability to solve thousands of hard riddles out there. When he just got back from his pilgrimage, Ngab found a note on his desk
    
    > O' dear my apprentice, if thou are back from thee pilgrim, can thou correct this wallpaper maker for me? It is a widescreen-ratio up to full hd one, this should be easy right?
    Thanks as always.
    > 
    
    **"Damn, you old man" Ngab says.**
    

---

#### 🔍 **Complete Solution**

---

**Step 1 : Cek tipe file yang diberikan**

Diberikan file `lemaoo.png` , kita coba cek terlebih dahulu apakah tipe filenya memang png.

```bash
file lemaoo.png
```

Output :

```bash
lemaoo.png: data
```

Ternyata tipe filenya bukan png.

kita coba cek bytes yang ada pada file tersebut.

```bash
xxd lemaoo.png | head
```

Output :

```bash
00000000: 8260 42ae 444e 4549 0000 0000 d56a fbfb  .`B.DNEI.....j..
00000010: 5341 13f2 01be df7f fabf ec30 f6b9 5ebf  SA.........0..^.
00000020: 6b47 e6ba facf bfbc 65f6 67ae 6daf 23d7  kG......e.g.m.#.
00000030: 39b5 637d 8fde a0bf cb73 9c8e 7785 7a9f  9.c}.....s..w.z.
00000040: b379 c45f 9e4a fad1 16e3 cfe9 1dd8 e932  .y._.J.........2
00000050: 3e61 7241 7dbf 2972 7754 cc99 1f2f 1d72  >arA}.)rwT.../.r
00000060: 8d10 9e49 de51 5e15 f52e 7cc2 9e40 2c38  ...I.Q^...|..@,8
00000070: bc57 9d78 f312 e4e9 50b2 2350 3799 2e1d  .W.x....P.#P7...
00000080: 6b5d 6ba8 0e77 5d84 e4bd 273e 39a2 732f  k]k..w]...'>9.s/
00000090: ff5b 3d49 7a91 ea23 4371 18fb 6118 1181  .[=Iz..#Cq..a...
```

```python
xxd lemaoo.png | tail
```

Output :

```python
00000150: 3c2c a9c8 130f d660 9d97 feae f037 5eed  <,.....`.....7^.
00000160: 8c57 fabb c0de 9b88 3e7f de1f c1cc c63f  .W......>......?
00000170: 064d 8c56 b5ad 6ba1 9326 1d7f a5dd b3fd  .M.V..k..&......
00000180: 98e5 872f def7 3f99 d96f 7b11 1111 1391  .../..?..o{.....
00000190: f92b e5b1 caf5 c0c6 8045 d2a9 c390 ca08  .+.......E......
000001a0: bab2 ffff e8ca 5144 0c30 c36e cbd4 ed9c  ......QD.0.n....
000001b0: 7854 4144 49a1 0100 001b 0e2b 9501 c40e  xTADI......+....
000001c0: 0000 c40e 0000 7359 4870 0900 0000 a322  ......sYHp....."
000001d0: de16 0000 0002 0860 0000 0058 0000 0052  .......`...X...R
000001e0: 4448 490d 0000 000a 1a0a 0069 ffd8 ff    DHI........i...
```

Struktur file di awali dengan DNEI (kebalikan dari IEND), yang artinya bytes dari file ini semuanya terbalik

---

**Step 2 : Fix The File**

Di sini kita membuat script python untuk membalik bytes dari file tersebut.

```python
file = open('lemaoo.png', 'rb').read()

fixed = open('reverse.png', 'wb').write(file[::-1])   
```

Script tersebut akan membuat file `reverse.png` dengan bytes dari file `lemaoo.png` yang sudah dibalik.

Kita coba cek lagi bytesnya apakah sudah benar apa belum.

```bash
xxd reverse.png | head
```

Output :

```python
00000000: ffd8 ff69 000a 1a0a 0000 000d 4948 4452  ...i........IHDR
00000010: 0000 0058 0000 0060 0802 0000 0016 de22  ...X...`......."
00000020: a300 0000 0970 4859 7300 000e c400 000e  .....pHYs.......
00000030: c401 952b 0e1b 0000 01a1 4944 4154 789c  ...+......IDATx.
00000040: edd4 cb6e c330 0c44 51ca e8ff ffb2 ba08  ...n.0.DQ.......
00000050: ca90 c3a9 d245 80c6 c0f5 cab1 e52b f991  .....E.......+..
00000060: 1311 1111 7b6f d999 3ff7 de2f 87e5 98fd  ....{o..?../....
00000070: b3dd a57f 1d26 93a1 6bad b556 8c4d 063f  .....&..k..V.M.?
00000080: c6cc c11f de7f 3e88 9bde c0bb fa57 8ced  ......>......W..
00000090: 5e37 f0ae fe97 9d60 d60f 13c8 a92c 3c0e  ^7.....`.....,<.
```

Bytesnya sudah tidak kebalik lagi, namun belum ada magic bytesnya, jadi kita tinggal timpa 8 byte pertama dengan hex signature dari png yaitu `89 50 4E 47 0D 0A 1A 0A` .

Setelah diperbaiki, file tersebut berisi gambar garis hitam putih.

![fixed.png](fixed.png)

Berdasarkan hint gambar yang hanya berisi warna hitam dan putih dapat merepresentasikan Biner dengan warna putih berarti 1 dan warna hitam berarti 0.

---

**Step 3 : Decode The Image**

Untuk merubah gambar menjadi binary, kita bisa menggunakan tools dari internet, di sini saya menggunakan tools dari dcode [https://www.dcode.fr/binary-image](https://www.dcode.fr/binary-image).

Jadi kita tinggal upload filenya, atur size dan witdhnya itu original size.

Setelah mendapatkan biner dan mencovertnya menjadi teks menggunakan cyber chef, kita mendapat url [https://tinyurl.com/m00nlander](https://tinyurl.com/m00nlander), jika kita mengunjungi url tersebut maka kita akan mendownload sebuah file bernama m00n

---

**Step 4 : Cek Tipe File m00n**

Kita coba cek hex dari file m00n tersebut.

```bash
xxd m00n | head
```

Output :

```bash
00000000: ffd8 ffe1 6969 1a0a 0000 000d 4948 4452  ....ii......IHDR
00000010: 0000 0000 0000 0000 0802 0000 00ce 13b2  ................
00000020: 6000 0000 0173 5247 4200 aece 1ce9 0000  `....sRGB.......
00000030: 0004 6741 4d41 0000 b18f 0bfc 6105 0000  ..gAMA......a...
00000040: fa7d 4944 4154 785e dcfd 8196 23c9 ad64  .}IDATx^....#..d
00000050: 01ae a6f5 ff5f ac7e b316 7e9d 9646 c0dd  ....._.~..~..F..
00000060: 23c8 acd6 9bdd 7b4a 21c0 6080 2382 4c66  #.....{J!.`.#.Lf
00000070: 5675 abf4 2ff8 ff0c feef fffd bfc4 5684  Vu../.........V.
00000080: c419 bdb0 cd81 5020 a408 2bba 3a2d 48c7  ......P ..+.:-H.
00000090: b0a4 771d cc42 d5ff f99f ffc9 993e bd37  ..w..B.......>.7
```

Tipe filenya PNG, tapi file headernya belum ada, jadi kita perbaiki file headernya agar sesuai dengan file header PNG dan ubah ekstensinya menjadi .png

Namun filenya masih tidak bisa dibuka, permasalahannya adalah width dan heightnya tidak ada, tapi CRC nya masih ada yaitu 0xce13b260.

> CRC32 adalah checksum 4 angka byte yang dihitung dari gabungan chunk IHDR + 4 byte (width) + 4 (byte height) + field IHDR lainnya (bit depth, color type, compression, filter, interface: total 5 byte)
> 

---

**Step 5 : Brute Force CRC**

Di chall ini, CRC nya masih ada dan tidak berubah walaupun width dan heightnya 0, karena CRC terbentuk oleh dimensi asli, kita tinggal brute force saja witdh dan heightnya lalu membandingkan CRC yang terbentuk dengan CRC yang ada pada file ini.

```python
import zlib, struct

crc = 0xce13b260
sisa_byte = bytes.fromhex('0802000000')

for W in range(1, 2001):
    for H in range(1, 2001):
        data = b'IHDR' + struct.pack(">II", W, H) + sisa_byte

        if zlib.crc32(data) == crc:
            print(f'W = {W}\nH = {H}')    
```

Dari script tersebut, jika CRC yang terbentuk dari brute force sama dengan CRC yang ada pada file tersebut, script tersebut akan mencetak witdh dan height yang asli.

> So basically karena CRC nya ada dan tidak berubah, kita tinggal bikin CRC yang baru dengan width dan height yang beragam, lalu nilai width dan height yang asli didapatkan jika CRC yang kita buat itu sama dengan CRC yang pada file  ini
> 

Output :

```python
W = 954
H = 1696
```

Width dan heightnya sudah didapatkan, kita tinggal ubah saja width dan height dari file png tersebut dengan width dan height yang sudah kita dapatkan.

Jika sudah, maka akan muncul gambar bulan dengan flag yang disamarkan di dalam gambar tersebut, jadi kita atur kecerahan dan kita pun akan mendapatkan flagnya! 

 

![final_flag.png](final_flag.png)

`COMPFEST14{hHhH_th0u_4re_c0Rr3ct!_634af16261}`