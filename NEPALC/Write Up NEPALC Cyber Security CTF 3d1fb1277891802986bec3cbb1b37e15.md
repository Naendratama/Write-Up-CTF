# Write Up NEPALC Cyber Security CTF

Web Exploitation

1. **Login Asadu**

![image.png](Asset/image.png)

1. Recon

![image.png](image%201.png)

Web memiliki login page, kita coba dulu weak credensial seperti admin;admin, admin;password

![image.png](image%202.png)

Kita berhasil login, namun kita tidak memiliki akses sebagai admin. 

Setelah enumerasi lebih lanjut, kita menemukan bahwa ada cookie yang berbentuk JWT token di web ini.

```jsx
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiYWRtaW4iLCJyb2xlIjoidXNlciIsImV4cCI6MTc4ODQ5NzAxNX0.F7ueP86malE48SIz0ZNGSg9eHKug4Bw4f9VYDM8XgVk
```

b.  Exploit

Setelah mendapatkan JWT tokennya, kita bisa mendecode JWT token tersebut di jwt.io

![image.png](image%203.png)

Setelah didecode, ternyata role dari user kita masih user, kita bisa mengganti value dari role tersebut menjadi admin.

![image.png](image%204.png)

```jsx
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiYWRtaW4iLCJyb2xlIjoiYWRtaW4iLCJleHAiOjE3ODg0OTcwMTV9.or1dstLuY434fSm5u_yHsQdzQfUeGogasVKu1wMB2FA
```

Langsung saja masukkan jwt token tersebut ke cookie!

![image.png](image%205.png)

Setelah kita mengganti value dari cookie-nya lalu refresh webnya, kita berhasil mendapatkan flag!

**`FLAG : NEPALC{JWT_BYPASS_AUTH_4DM1N}`**

1. **Broken Calculator**

![image.png](image%206.png)

1. Recon
    
    ![image.png](image%207.png)
    
    Web ini adalah web kalkulator sederhana pada umumnya, jika kita menginput 7*7 maka outputnya adalah 49
    
    Kita bisa menggunakan perintah curl untuk mengetahui web ini terbuat dari bahasa apa
    
    ```jsx
    curl -v http://202.46.150.52:8082/
    ```
    
    ![image.png](image%208.png)
    
    Ternyata web ini terbuat dari bahasa php, dan jika kita memasukkan index.php di url webnya, maka web akan merespons dengan normal.
    
2. Exploit

Ada banyak sekali kerentanan pada php, contohnya adalah SSTI, coba kita input payload umum SSTI php.

```jsx
system('id')
```

![image.png](image%209.png)

Ternyata kita tidak bisa memasukkan system(’id’), coba kita input huruf asal asalan

![image.png](image%2010.png)

Ternyata outputnya tidak sama dengan output dari system(’id’), sepertinya di web ini ada WAF nya

[https://tutorialboy.medium.com/bypassing-php-waf-to-achieve-remote-code-execution-in-depth-analysis-f42eb6e633fa](https://tutorialboy.medium.com/bypassing-php-waf-to-achieve-remote-code-execution-in-depth-analysis-f42eb6e633fa)

Dari URL tersebut, kita bisa membypass WAF nya dengan mengencode “system” ke hex dengan delimiter \x

```jsx
"\x73\x79\x73\x74\x65\x6d"('whoami')
```

![image.png](image%2011.png)

Dan sepertinya kita berhasil mendapatkan RCE nya!

Karena flagnya tidak ketemu ketemu, kita coba cek env dari web ini

```jsx
"\x73\x79\x73\x74\x65\x6d"('env')
```

![image.png](image%2012.png)

Ternyata flag ada di env!

**FLAG `NEPALC{R3G3X_BYP455_1S_VERYYY_E4SY}`**

1. Ketuk Pintu

![image.png](image%2013.png)

1. Recon
    
    ![image.png](image%2014.png)
    
    Web ini hanya html static dan tidak ada fitur apapun, setelah enumerasi lebih lanjut ternyata web ini memakai php, bisa dibuktikan dengan memasukkan index.php ke url nya
    
2. Exploit
    
    Setelah coba beberapa kali kemungkinan vuln, ternyata kita bisa memasukkan parameter ?id di dalam url nya
    
    ![image.png](image%2015.png)
    
    Dipastikan bahwa ini adalah IDOR, jadi kita bisa memasukkan nilai 1 pada parameter ?id
    
    `index.php?id=1`
    
    ![image.png](image%2016.png)
    
    Flag ditemukan!
    
    **FLAG: `FLAG{IDOR_S_S_S_First_Step_Success}`**
    

**Cryptography**

1. **The Ancient Message**
    
    ![image.png](image%2017.png)
    
    1. Recon
        
        Chall memberikan file cipher.txt yang isinya 
        
        ```jsx
        Zkzhz sh yzotzo yzotzo srz wts, aol mshn pz ULWHSJ{J43Z4Y_J1WO3Y_I4Z1J}
        ```
        
        Coba kita cek jenis cipher apakah ini menggunakan web cipher identifier
        
        ![Screenshot 2026-09-04 130357.png](Screenshot_2026-09-04_130357.png)
        
        Hasilnya menunjukkan cipher ini kemungkinan besar antara ROT cipher dan Caesar cipher, kita coba dulu decrypt di web caesar cipher decoder.
        
        ![image.png](image%2018.png)
        
        Ternyata jenis cipher tersebut adalah caesar cipher dengan shift 7!
        
        **FLAG: `NEPALC{C43S4R_C1PH3R_B4S1C}`** 
        
2. The secret ke
    
    ![image.png](image%2019.png)
    
    1. Recon
        
        Chall memberikan file cipher2.txt yang isinya
        
        ```jsx
        Lpw liojml ypmy ngk xaviq bw ZWXSEG{H1Y3V_K1FTX3_K3KJM}
        	
        ```
        
        Kita cari jenis ciphernya di web cipher identifier
        
        ![image.png](image%2020.png)
        
        Jenis ciphernya adalah Vigenere Cipher, di mana kita tidak dikasih key untuk mendecryptnya.
        
        b. Exploit
        
        Di sini kita brute force huruf dari key-nya sampai cocok dengan format flag NEPALC, setelah brute force manual, kita menemukan bahwa key-nya adalah msiste yang membuat kita mendapatkan flagnya! 
        
        ![image.png](image%2021.png)
        
        **FLAG : `NEPALC{V1G3N_S1MPL3_S3CRT}`**
        
3. Binary Mastery
    
    ![image.png](image%2022.png)
    
    Diberikan file cipher2.txt yang isinya adalah kumpulan dari hex, dan dikasih tau juga ada key ‘Sakti’
    
    ```jsx
    04 04 07 17 06 3e 04 4b 00 06 73 15 03 11 49 0b 2e 39 54 0a 3b 00 07 18 0c 3d 06 0e 5a 49 07 09 0e 54 0f 3f 00 0c 54 00 20 41 25 31 39 12 2d 28 0f 31 63 33 34 38 59 10 55 27 2b 2b 62 2f 5f 26 30 2e
    
    ```
    
    1. Recon
        
        Karena isi file adalah kumpulan hex dan kita diberi key ‘Sakti’, kita bisa beranggapan bahwa kita harus mendecrypt hex tersebut lalu menggunakan XOR dengan key ‘Sakti’ untuk mendapatkan flagnya.
        
    2. Exploit
        
        Kita bisa menggunakan cyber chef dengan urutan ‘from hex’ lalu ‘XOR’ dengan key ‘Sakti’
        
        ![image.png](image%2023.png)
        
        **FLAG** : `NEPALC{X0R_L0C4L_B1N4RY}`
