# Cuking

---

#### 📋 Challenge Description

- **Category : Forensics**
- **Point : 50**

Baim: “Aneh deh, katanya foto ini penting banget buat penyelidikan, tapi pas aku buka cuma gambar biasa.” Rudi: “Serius? Coba cek lebih teliti. Kadang orang nyimpen sesuatu di tempat yang nggak kelihatan.”

---

**Step 1 : Enumerasi Awal (Cek tipe file, buka filenya, cek metadata)**

Diberikan file **`cat.jpg` ,** kita coba cek tipe dari file tersebut.

```bash
file cat.jpg
```

Output :

```bash
cat.jpg: JPEG image data, JFIF standard 1.01, resolution (DPI), density 72x72, segment length 16, Exif Standard: [TIFF image data, big-endian, direntries=5, xresolution=74, yresolution=82, resolutionunit=2], comment: "109 51 116 97 100 52 116 97 95 104 117 110 116 114 95 102 48 114 51 110 115 105 107", progressive, precision 8, 736x736, components 3
```

Tidak ada keanehan, coba kita buka isi dari file cat.jpg ini

![cat.jpg](Image/cat.jpg)

Tidak ada keanehan juga, hanya foto kucing meme senyum bilek biasa.

Lanjut, kita coba cek metadata dari file tersebut.

```bash
exiftool cat.jpg
```

Output :

```bash
ExifTool Version Number         : 13.55
File Name                       : cat.jpg
Directory                       : .
File Size                       : 50 kB
File Modification Date/Time     : 2026:10:09 06:17:56+07:00
File Access Date/Time           : 2026:10:17 12:32:54+07:00
File Inode Change Date/Time     : 2026:10:17 12:32:49+07:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Exif Byte Order                 : Big-endian (Motorola, MM)
X Resolution                    : 72
Y Resolution                    : 72
Resolution Unit                 : inches
Y Cb Cr Positioning             : Centered
Profile CMM Type                :
Profile Version                 : 4.3.0
Profile Class                   : Display Device Profile
Color Space Data                : RGB
Profile Connection Space        : XYZ
Profile Date Time               : 2016:01:01 00:00:00
Profile File Signature          : acsp
Primary Platform                : Unknown ()
CMM Flags                       : Not Embedded, Independent
Device Manufacturer             :
Device Model                    :
Device Attributes               : Reflective, Glossy, Positive, Color
Rendering Intent                : Media-Relative Colorimetric
Connection Space Illuminant     : 0.9642 1 0.82491
Profile Creator                 :
Profile ID                      : 0
Profile Description             : sRGB
Red Matrix Column               : 0.43607 0.22249 0.01392
Green Matrix Column             : 0.38515 0.71687 0.09708
Blue Matrix Column              : 0.14307 0.06061 0.7141
Media White Point               : 0.9642 1 0.82491
Red Tone Reproduction Curve     : (Binary data 40 bytes, use -b option to extract)
Green Tone Reproduction Curve   : (Binary data 40 bytes, use -b option to extract)
Blue Tone Reproduction Curve    : (Binary data 40 bytes, use -b option to extract)
Profile Copyright               : Google Inc. 2016
Comment                         : 109 51 116 97 100 52 116 97 95 104 117 110 116 114 95 102 48 114 51 110 115 105 107
Image Width                     : 736
Image Height                    : 736
Encoding Process                : Progressive DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 736x736
Megapixels                      : 0.542
```

Di comment terdapat beberapa bilangan, di mana bilangan tersebut mungkin adalah sebuah bilangan decimal.

```bash
109 51 116 97 100 52 116 97 95 104 117 110 116 114 95 102 48 114 51 110 115 105 107
```

---

**Step 2 : Decode Bilangan Decimal**

DI sini langsung saja kita decode bilangan decimal tersebut menjadi karakter ASCII

```bash
dec = [109,51,116,97,100,52,116,97,95,104,117,110,116,114,95,102,48,114,51,110,115,105,107]

string = ""

for decs in dec:
    string += chr(decs)

print(string) 
```

Output :

```bash
m3tad4ta_huntr_f0r3nsik
```

Karena format flagnya **`ITFEST{...}` ,** kita masukkan output tersebut ke dalam format flagnya.

**`ITFEST{m3tad4ta_huntr_f0r3nsik}`**
