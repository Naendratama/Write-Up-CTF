# Operation Oni

---

### 🗒️ **Challenge Description**

---

Download this disk image, find the key and log into the remote machine.

Note: if you are using the webshell, download and extract the disk image into **`/tmp`** not your home directory.

- Remote machine: **`ssh -i key_file -p 10493 ctf-player@xebec.cylabacademy.net`**
- Download file image `link download filenya`
- Category : Forensic
- Difficulty : Medium

---

#### 🔍 Solution

---

**Step 1 : Mengidentifikasi Tipe File**

Diberikan file **disk.img**, kita coba cek tipe dari file tersebut.

```bash
file disk.img
```

Output yang diberikan

```bash
disk.img: DOS/MBR boot sector; partition 1 : ID=0x83, active, start-CHS (0x0,32,33), end-CHS (0xc,223,19), startsector 2048, 204800 sectors; partition 2 : ID=0x83, start-CHS (0xc,223,20), end-CHS (0x1d,81,52), startsector 206848, 264192 sectors
```

Dari tipenya, file ini adalah file disk image dari sebuah hardisk.

---

**Step 2 : Mengecek partisi yang ada beserta isinya**

Karena file tersebut adalah disk image dari sebuah hardisk, kita coba cek partisi apa saja yang ada di file ini.

```bash
mmls disk.img
```

Output yang diberikan.

```bash
DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
001:  -------   0000000000   0000002047   0000002048   Unallocated
002:  000:000   0000002048   0000206847   0000204800   Linux (0x83)
003:  000:001   0000206848   0000471039   0000264192   Linux (0x83)
```

Terdapat 2 partisi linux, kita coba cek isi dari partisi linux dengan sektor 2048.

```bash
fls -o 2048 -r disk.img
```

Output :

```bash
d/d 11: lost+found
r/r 12: ldlinux.sys
r/r 13: ldlinux.c32
r/r 15: config-virt
r/r 16: vmlinuz-virt
r/r 17: initramfs-virt
l/l 18: boot
r/r 20: libutil.c32
r/r 19: extlinux.conf
r/r 21: libcom32.c32
r/r 22: mboot.c32
r/r 23: menu.c32
r/r 14: System.map-virt
r/r 24: vesamenu.c32
V/V 25585:      $OrphanFiles
```

Sepertinya tidak ada yang aneh di dalam partisi linux ini, dan sepertinya partisi utama ada di sektor 206848, jadi langsung saja kita mount partisi linux sektor 206848 tersebut.

```bash
sudo mount -o loop,offset=105906176 disk.img mount
```

> Nilai offset 105906176 didapatkan dari hasil perkalian :  Start Sector (206848) * Ukuran Sector  (512)
> 

Langsung saja masuk ke dalam direktori mount, dan list file/direktori yang ada.

```bash
┌──(root㉿NarenTzy)-[/home/naren/ctf/picoCTF/foren/Operation Oni/mount]
└─# ls
bin  boot  dev  etc  home  lib  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

Terdapat direktori root, kita coba masuk ke direktori root tersebut dan lihat apa saja yang ada di dalam direktori tersebut.

```bash
┌──(root㉿NarenTzy)-[/home/naren/ctf/picoCTF/foren/Operation Oni/mount/root]
└─# ls -la
total 4
drwx------  3 root root 1024 Oct  6  2021 .
drwxr-xr-x 21 root root 1024 Oct  6  2021 ..
-rw-------  1 root root   36 Oct  6  2021 .ash_history
drwx------  2 root root 1024 Oct  6  2021 .ssh
```

Terdapat direktori .ssh, dan kebetulan dari deskripsi chall diberi alamat ssh untuk login, jadi mungkin saja kita harus login ke alamat ssh yang diberikan menggunakan kredensial/file private key. Kita coba lihat isi dari direktori .ssh

```bash
┌──(root㉿NarenTzy)-[/home/naren/ctf/picoCTF/foren/Operation Oni/mount/root/.ssh]
└─# ls -la
total 4
drwx------ 2 root root 1024 Oct  6  2021 .
drwx------ 3 root root 1024 Oct  6  2021 ..
-rw------- 1 root root  411 Oct  6  2021 id_ed25519
-rw-r--r-- 1 root root   96 Oct  6  2021 id_ed25519.pub
```

Benar saja ternyata ada 2 file private key dan public key. Kita ubah terlebih dahulu permission file private keynya menjadi 700 (syarat file yang bisa digunakan untuk login ssh adalah permissionnya bernilai 700)

```bash
chmod 700 id_ed25519
```

Langsung saja login ke ssh yang diberikan menggunakan file private key yang sudah kita ubah permissionnya tadi.

```bash
ssh -i id_ed25519 -p 44914 ctf-player@chatelaine.cylabacademy.net
```

Output :

```bash
The authenticity of host '[chatelaine.cylabacademy.net]:44914 ([18.227.187.235]:44914)' can't be established.
ED25519 key fingerprint is: SHA256:ZzFWPkrkkhq4v3aMejPdeBM6CxOJEdDfCOqwc0eXgfY
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[chatelaine.cylabacademy.net]:44914' (ED25519) to the list of known hosts.
Welcome to Ubuntu 24.04.4 LTS (GNU/Linux 7.0.0-1013-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ctf-player@challenge:~$ 
```

Dan kita sudah berhasil login ke sshnya !, langsung saja list file yang ada di direktori terkini.

```bash
ls -la
```

Output :

```bash
total 4
drwxr-xr-x 1 ctf-player ctf-player 20 Sep 30 12:51 .
drwxr-xr-x 1 root       root       24 Sep 23 02:58 ..
drwx------ 2 ctf-player ctf-player 34 Sep 30 12:51 .cache
drwxr-xr-x 2 ctf-player ctf-player 29 Sep 23 02:58 .ssh
-rw-r--r-- 1 root       root       28 Sep 23 02:58 flag.txt
```

Dan ternyata ada file flag.txt yang berisi flag dari chall ini !

**`academy{k3y_5l3u7h_0e000cd7}`**