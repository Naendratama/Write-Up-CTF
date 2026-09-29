# 🚩 flags are stepic

---

**Challenge Description**

---

A group of underground hackers might be using this legit site to communicate. Use your forensic techniques to uncover their message.

- Category: Forensics
- Difficulty: Hard
- Hint: **In the country that doesn't exist, the flag persists**

---

**Complete Solution**

---

**Step 1 : Visit Website yang diberikan**

Diberikan sebuah link website, langsung saja visit websitenya 

```
http://xebec.cylabacademy.net:39513/
```

![image.png](../Images/flags-are-stepic-1.png)

Web hanya berisi gambar bendera dari berbagai negara, tidak ada fitur apapun yang ada di web ini. Berdasarkan hint yang diberikan, kita diberi tahu bahwa flag ada di bendera dari negara yang tidak ada.

---

**Step 2 : Mencari bendera dari negara yang tidak ada**

![image.png](../Images/flags-are-stepic-2.png)

Setelah kita scroll webnya, kita menemukan sebuah gambar bendera dari negara Upanzi yang merupakan negara palsu/tidak ada.

---

**Step 3 : Mendownload file gambar negara Upanzi**

```html
{ name: "Upanzi, Republic The",img: "flags/upz.png", style:"width: 120px!important; height: 90px!important;" },
```

Setelah dicek source codenya, file dari gambar bendera negara Upanzi tersebut ada di endpoint **/flags/upz.png.** Jadi langsung saja download file dari endpoint tersebut menggunakan wget.

```bash
wget http://xebec.cylabacademy.net:39513/flags/upz.png
```

Setelah didownload kita akan mendapatkan file bernama “upz.png”

---

**Step 4 : Mencari strings yang ada di file**

Di sini, saya sudah mencoba command seperti strings, exiftool, zsteg, binwalk, namun masih belum mendapatkan flag. Lalu saya teringat bahwa ada kosakata yang ada pada judul chall yang tidak saya pahami yaitu “**stepic**” dan menemukan url ini.

[https://pypi.org/project/stepic/](https://pypi.org/project/stepic/)

Dari URL tersebut, kita mengetahui bahwa stepic adalah module dari python untuk menyembunyi-kan data di file gambar, langsung saja install module python tersebut di terminal kita.

```bash
pip install stepic
```

Setelah diinstall, langsung saja kita gunakan perintah di bawah ini untuk mengesktrak data yang tersembunyi pada file gambar ini.

```bash
stepic -i upz.png -d
```

- -i untuk membaca file image untuk decode atau encoding dari data yang tersembunyi
- -d untuk mendecode data tersembunyi dari file tersebut

Setelah perintah tersebut dijalankan, maka kita akan mendapatkan flag.

`picoCTF{fl4g_h45_fl4g0e590975}`
