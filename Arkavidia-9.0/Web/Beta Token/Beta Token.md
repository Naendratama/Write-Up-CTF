# Beta Token

#### **Challenge Description**

---

- **Category : Web Exploitation**
- **Difficulty : Baby**

Zagreus meet Sisyphus on his way to Olympus and befriended Him. One day, Sisyphus shared a secret to Zagreus. He claimed that this secret was told to him by his Boulder. When Zagreus heard of this secret he merely said, "What a weak secret, anyone can guess that.”

![image.png](image.png)

---

**Step 1 : Visit Web**

![image.png](image%201.png)

Web memiliki 3 page, yaitu home, flag dan login. Kita coba visit page flag-nya.

![image.png](image%202.png)

Sepertinya dibutuhkan akses admin untuk mengakses halaman flag yang sebenarnya, yasudah kita coba login terlebih dahulu.

---

**Step 2 : Login Ke Web**

![image.png](image%203.png)

Kita coba login dengan kredensial user admin dan password-nya admin, dan ternyata kita berhasil login, kita coba akses page flagnya.

![Screenshot 2026-10-20 103353.png](Screenshot_2026-10-20_103353.png)

Masih tidak bisa, kita coba cek cookie yang ada di web ini, ternyata ada cookie berbentuk JWT Token, kita coba decode JWT token tersebut.

![image.png](image%204.png)

Dan role kita masih USER, walaupun kita sudah berhasil login dengan username admin, di descripsi chall menyebutkan “weak secret”, dan gif nya pun tentang “rock” yang mungkin berhubungan dengan wordlists rockyou, jadi dapat disimpulkan kita perlu membrute force secret dari JWT tersebut dengan wordlists rockyou.txt

---

**Step 3 : Brute Force Secret JWT Token**

Di sini kita bisa menggunakan tools JWT Cracker untuk membrute force secret dari JWT Token tersebut.

```bash
jwt-cracker -t eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiVVNFUiIsImlhdCI6MTc5MjQ2NzU4NSwiZXhwIjoxNzkyNDcxMTg1fQ.iRcU8rVyfcldJo1wA36KUNw1P5JASJHrdhLtbXakTFo -d /usr/share/wordlists/rockyou.txt
```

Output :

```bash
SECRET FOUND: 12341234
Time taken (sec): 0.157
Total attempts: 20000
```

Di sini secretnya sudah ketemu yaitu “12341234””, langsung saja kita ubah claim role dari JWT Token tadi menjadi ADMIN dengan secret yang baru saja kita dapatkan.

![image.png](image%205.png)

---

**Step 4 : Request ulang ke web nya menggunakan JWT Token yang sudah dibuat**

JWT Tokennya sudah dibuat, langsung saja request ulang ke webnya menggunakan JWT Token yang baru saja kita buat.

![image.png](image%206.png)

FLAG : **`ARKAV{g00d_lUck_0n_Th3_N3xt_Ch4ll3ng3_14923857109213}`**