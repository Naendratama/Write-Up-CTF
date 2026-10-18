# GERBANG OPERATOR RAHASIA

---

#### 📋 **Challenge Description**

- **Category : Misc**
- **Point : 116**

Sistem keamanan ini hanya menerima akses jika kamu membuktikan kepemilikan akun GitHub

---

**Step 1 : Enumerasi Web**

![Screenshot 2026-10-17 181800.png](Screenshot_2026-10-17_181800.png)

Di sini web hanya memiliki 1 halaman saja, terdapat fitur untuk input Username dan tombol generate jika dipencet akan menghasilkan string acak atau nonce.

![image.png](image.png)

Terdapat pula fitur verify, dimana fitur tersebut membutuhkan sebuah SSH Signature untuk menghasilkan output. Jika SSH Signature yang diinput salah, maka output nya adalah **“Invalid Signature”.**

![Screenshot 2026-10-17 182022.png](Screenshot_2026-10-17_182022.png)

Di sini, kita coba intercept request saat generate nonce-nya, dan menemukan terdapat key bernama **`namespace`** yang memiliki value **`ctfd-auth`**

---

**Step 2 : Generate SSH Signature**

**kepemilikan akun GitHub, SSH Signature,** kedua hal tersebut mungkin berarti SSH Signature dari public key akun Github, jadi dapat disimpulkan kita disuruh untuk membuat SSH Signature dari public-key akun github beserta nonce yang tadi kita dapatkan dari menginput username akun Github.

Di sini kita sudah mempunyai file public-key dan private-key dari akun github kita.

File Public Key :

```bash
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMdPF6Mt9ztoFhYtCFaXyQcCDa8VKtZreyB7i9/lNGgX naren@Narentzy
```

File Private Key:

```bash
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
QyNTUxOQAAACDHTxejLfc7aBYWLQhWl8kHAg2vFSrWa3sge4vf5TRoFwAAAJiWLN8Nlizf
DQAAAAtzc2gtZWQyNTUxOQAAACDHTxejLfc7aBYWLQhWl8kHAg2vFSrWa3sge4vf5TRoFw
AAAEDfN9n0AjG0yCTMcjjSNrFnokyvZ0D8gJ3SwtRxsyGIrsdPF6Mt9ztoFhYtCFaXyQcC
Da8VKtZreyB7i9/lNGgXAAAADm5hcmVuQE5hcmVudHp5AQIDBAUGBw==
-----END OPENSSH PRIVATE KEY-----
```

Pertama tama, kita simpan nonce yang tadi kita dapat ke file bernama **`tes.txt`** 

```bash
echo -n "yTrmIlrVVJj-XMwCHKICeJPduKyma2Wgr4XPdsvph4s" > tes.txt
```

Setelah itu kita buat SSH Signaturenya menggunakan private-key dari akun github kita, lalu file yang berisi nonce yang tadi kita dapatkan. Untuk namespace-nya kita gunakan value dari namespace yang kita dapatkan dengan mengintercept request saat generate nonce-nya.

```bash
ssh-keygen -Y sign -f id_ed25519 -n ctfd-auth tes.txt
```

Jika sudah, maka akan muncul bernama tes.txt.sig

```bash
-----BEGIN SSH SIGNATURE-----
U1NIU0lHAAAAAQAAA[REDACTED]
-----END SSH SIGNATURE-----
```

---

**Step 3 : Input SSH Signature**

SSH Signature sudah kita dapatkan, langsung saja kita input ke kolom signature dan klik verify.

![Screenshot 2026-10-17 181953.png](Screenshot_2026-10-17_181953.png)

Di sini usernamenya yang kita masukkan harus sesuai dengan username dari akun github kita.

![Screenshot 2026-10-17 182004.png](Screenshot_2026-10-17_182004.png)

**`ITFEST{55h_k3yg3n_51gn1ng_15_4w350m3_b077a2}`**