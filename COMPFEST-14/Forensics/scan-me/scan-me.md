# scan-me

#### 📋 **Challenge Desription**

- **Category : Forensics**
- **Difficulty: Easy**
- **Hint : The first 8-bit seems familiar, right ?**

Jack and his uncle loves to solve puzzle. This morning, Jack's Uncle gives him a stack of card and said:

" in order to solve this, don't scramble the card, scramble the egg "

" Just kidding "

" Bye, shift you latter "

---

**Step 1 : Scan QR Code**

Diberikan file `qr.zip` , yang jika kita extract akan menghasilkan 828 gambar PNG yang semuanya berisi QR code, langsung saja kita scan semua QR code yang ada pada setiap file gambar tersebut.

```python
from PIL import Image
from pyzbar.pyzbar import decode

string = ""

for i in range(1, 828):
    gambar = Image.open(f"{i}.png")
    hasil_scan = decode(gambar)

    data_bytes = hasil_scan[0].data
    data_string = data_bytes.decode("utf-8")

    string += data_string

with open('data.txt', 'w') as f:
    f.write(string)
    
with open('data.txt', 'r') as f:
    kode = f.read().strip()
    enc = kode.replace("0x", "")

    with open('decode.txt', 'w') as w:
        w.write(enc) 
```

Script di atas akan mengscan semua QR code yang ada pada setiap 828 gambar, lalu hasil scan tersebut akan dimasukkan ke sebuah file bernama `data.txt` , lalu ternyata ada string `0x` sehingga kita perlu menghapus `0x` tersebut, setelah dihapus outputnya akan dimasukkan ke file bernama decode.txt

Output :

```bash
ghd_ctcf_s_p_p_p_______schcgccda_____`ra_____`ra_`________dcebuctg_____`_bchccc`dcfgh
rtshrcsetpcb_`_gddu_scgqsbcbfrgd`rrhdfb_bbgcp`ua_`aarabg`stardrqeg`ec`aahcbcqehucepg`
crbqhuapashdtprfddetabftaetbhu`ugag_cgh`aacdgp`cahdapccgh`aacdgpurcgrttesatf`stqq``pb_qf
__te`efhqubscupfrggqh`_schharhegceqbecbtsegfffqfpqcghahrrtscahua`aecdqhf_ebccqdprdph_bg`
usshs`sr_`u`ud_chsaaaucuptgeq`eqtgaqeqg`u`edfhq_cth`hfaarbtaguq`eqegbs__rcthr`bpcdbtbbq
hghedbhfabdqcbgprastbssg_bghcusc___ub_hbpcddthcdrdbshhd_qq``qpscfcbcuprfbhtesseusgtuude
tcauaar`pbcr_sg`c_fpa`cepesfpb_rruudbscdbththrbspgdh_u__u`ethtctabqudpguddftcgtcefdabs
_q`_`ef___r__`_sus``cg_te`bhutq`urh_rgaahq_g`eqhugd`eaqdhrucshscpctbrdqca``q`aqhhbercb__
rhtrgfcpdaqcbbpt_dagtfshqaa`h`cdpp_sa`hacffqchshbdh__schbeacuapbtcsgqcerurh_sfedhb_fpuh
cagh`sqdpcsbeacqargaepfctftegcactg_qgpcpgqegsfcrpgfccutgqhrrs__fgchgespbccqpsqac_hphd
serfdhbpcddtgbbrrebagudqsduqcrrgeb`udrdcsqgqurgahreubctq___tubrs`qefstfbfdqfhcgt`frft
tcchsaapuc`etrtgfuragb`arpggfgpuhqddcuhhbtpbah`cfshtescahgabdhtdshfqabtsfeppptscdftc
hpt_egc_deeau_qb_fepq_`qbdhbd`uduhsppdbtcpdrghr_dfhaefseuube`fd`prrqub`_sg_erpes`htubfq
gpbrhusruerqsaf`fcdpr``scusr_fqhbp_dpadgaebarqbepf_efasubttctteqthhfddhbha_sghsrchhhgd
dsgs_geupsa`qruhefebbfr`daaebsffb___t_sgcsuupudbtcqdrgcute`gcqsu`tfdpddgaeuadqqastfbr
srdhhqfdqt`u`rsupsbfdgpqr`gdhttqh_ac_hruabdthq`fbubt`g_sd`_tuqpaqasuaag`utpbafphbbd_ug
_fgtf_redhrcgq`crtcffecehqfstusgppqudrhcqqs`qgfdhftcgtcufpcsuh_qeucqq`uth_`scpq`ugus_
ahuaghrhcuttqh_a`ehhtegf`egqpubf`praqqs`bfcts_ahuadptcfeht`pc_tpeetrppfr__uqccchuhh_rg
hsecbu`cppgbahpqr_rs_edbctfsgecth`aacdgp`cahdapccgh`aacdgp`cdhtdauc_uft_qga_dcprgf_____
___chcdctccptcae_
```

---

**Step 2 : Decode Outputnya**

Dari deskripsi chall yaitu **`shift you later` ,** sepertinya output sudah diencode menggunakan teknik pergeseran huruf seperti ROT13, ROT47, Caesar Cipher, dan Vigenere Cipher.

![image.png](image.png)

Ketika kita coba untuk mendecode menggunakan ROT47, baris pertama dari output seperti menunjukkan sebuah deretan angka yang familiar dengan byte yang menjadi magic bytes dari file signature. Di sini kita simpan hasil outputnya ke file `rot47.txt`

---

**Step 3 : Generate File PNG** 

Di sini kita coba generate file PNG menggunakan isi dari file `rot47.txt` 

```bash
with open('rot47.txt', 'r') as f:
    kode = f.read().strip()

    data_hex = bytes.fromhex(kode)

    with open('hasil.png', 'wb') as w:
        w.write(data_hex)
```

Script ini akan membuka isi dari file `rot47.txt`, lalu isi dari file tersebut dijadikan deretan asli byte yang disimpan di variabel `data_hex`, lalu script membuat file `hasil.png` di mana file tersebut diisi bytes dari variabel `data_hex` .

Setelah itu akan muncul file PNG yang berisi QR Code yang terpotong.

![hasil.png](hasil.png)

---

**Step 4 : Fix The Error**

Di sini kita coba lihat bytes dari file gambar QR Code yang terpotong itu.

```bash
xxd hasil.png | head
```

Output :

```bash
00000000: 8950 4e47 0d0a 1a0a 0000 000d 4948 4452  .PNG........IHDR
00000010: 0000 01c2 0000 01c2 0100 0000 0054 63f4  .............Tc.
00000020: e800 0001 0349 4441 5478 9ced 9c4d 6ea4  .....IDATx...Mn.
00000030: 3010 855f 0d48 bd34 37c8 51cc 9573 0338  0.._.H.47.Q..s.8
00000040: 4a1f 2012 2c23 81de 2c5c b681 6412 2943  J. .,#..,\..d.)C
00000050: 4b69 f46a 814c 3b9f 2a2d 95ea c755 6e23  Ki.j.L;.*-...Un#
00000060: 7e26 e39f 1f82 8048 9122 458a 1429 52a4  ~&.....H."E..)R.
00000070: 4891 2245 8afc 48ce e6d2 e71d ebb1 1a30  H."E..H........0
00000080: b700 e616 79bf 3d4f a7c8 8b91 0d49 92c9  ....y.=O.....I..
00000090: 6846 b364 3ed6 8777 b7ab 4892 9cce d429  hF.d>..w..H....)
```

Magic bytes ada dan benar, CRC IHDR nya juga sudah benar.

Kita coba lihat ukuran dari file `hasi.png` 

```bash
ls -l
```

Output :

```bash
-rw-r--r-- 1 naren naren    827 Oct 16 08:29 hasil.png
```

Kita coba hitung LENGTH IDAT yang seharusnya

```bash
827 - 57 = 770
```

> 57 didapatkan dari penjumlahan 8 signature + 25 IHDR + 12 pembungkus IDAT + 12 IEND
> 

Terdapat kesalahan deklarasi LENGTH IDAT pada file yang seharusnya itu 770 `(00 0003 02)` , namun tertulisnya 259 `(00 0001 03)` , setelah kita perbaiki LENGTH IDAT nya maka gambar QR Code terpotong tersebut akan menjadi utuh.

![haha.png](haha.png)

Jika kita scan akan menghasilkan flag
**`COMPFEST14{Th1s_1S_Th3_c0rrr3ct_Sc4n_M3_Fl4g_5425470cc0}`**