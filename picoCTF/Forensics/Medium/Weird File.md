# Weird File

---

### **🗒️ Challenge Description**

---

- **Category:** Forensics
- **Difficulty**: Medium
- **Hint**: [**https://www.youtube.com/watch?v=Y7IJjnLGqTQ**](https://www.youtube.com/watch?v=Y7IJjnLGqTQ)

What could go wrong if we let Word documents run programs? (aka "in-the-clear"). [**weird.docm**](https://challenge-files.cylabacademy.net/library/b99ccd7c322df5b671439cb3a077a65446b967013fe4a3036610b53e0d311bca/weird.docm)

---

#### 🔍 Complete Solution

---

**Step 1 : Mengidentifikasi File**

Diberikan file **`weird.docm` ,** kita identifikasi terlebih dahuli tipe filenya.

```bash
file weird.docm
```

Output :

```bash
weird.docm: Microsoft Word 2007+
```

Berdasarkan outputnya, diketahui bahwa tipe filenya adalah Microsoft Word.

---

**Step 2 : Mencari maksud dari hint** 

Dari isi video youtube yang diberikan sebagai hint di mana video tersebut membahas tentang `"macro-based Office attacks"` , sepertinya chall ini berkaitan dengan isi dari video youtube tersebut yaitu `macro-based Office attacks` . Setelah searching, saya menemukan url ini 

[https://socfortress.medium.com/malicious-macros-detection-in-ms-office-files-using-olevba-752ed6b48c04](https://socfortress.medium.com/malicious-macros-detection-in-ms-office-files-using-olevba-752ed6b48c04)

Dari URL tersebut, kita mengetahui ada tools bernama `olevba` yang bisa mendeteksi serangan `"macro-based Office"` 

---

**Step 3 : Mendeteksi macro-based Office** 

Di sini kita install terlebih dahulu tools olevba-nya.

```bash
sudo apt install olevba
```

Jika install sudah selesai, langsung saja gunakan tools tersebut untuk mendeteksi macro-based office attack pada file weird.docm.

```bash
olevba weird.docm
```

Output :

```bash
olevba 0.60.2 on Python 3.14.7 - http://decalage.info/python/oletools
===============================================================================
FILE: weird.docm
Type: OpenXML
WARNING  For now, VBA stomping cannot be detected for files in memory
-------------------------------------------------------------------------------
VBA MACRO ThisDocument.cls
in file: word/vbaProject.bin - OLE stream: 'VBA/ThisDocument'
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Sub AutoOpen()
    MsgBox "Macros can run any program", 0, "Title"
    Signature

End Sub

 Sub Signature()
    Selection.TypeText Text:="some text"
    Selection.TypeParagraph

 End Sub

 Sub runpython()

Dim Ret_Val
Args = """" '"""
Ret_Val = Shell("python -c 'print(\"cGljb0NURnttNGNyMHNfcl9kNG5nM3IwdXN9\")'" & " " & Args, vbNormalFocus)
If Ret_Val = 0 Then
   MsgBox "Couldn't run python script!", vbOKOnly
End If
End Sub
+----------+--------------------+---------------------------------------------+
|Type      |Keyword             |Description                                  |
+----------+--------------------+---------------------------------------------+
|AutoExec  |AutoOpen            |Runs when the Word document is opened        |
|Suspicious|Shell               |May run an executable file or a system       |
|          |                    |command                                      |
|Suspicious|vbNormalFocus       |May run an executable file or a system       |
|          |                    |command                                      |
|Suspicious|run                 |May run an executable file or a system       |
|          |                    |command                                      |
+----------+--------------------+---------------------------------------------+
```

Berdasarkan output terdapat string base64 yang mencurigakan `cGljb0NURnttNGNyMHNfcl9kNG5nM3IwdXN9`**,** jika kita decrypt maka kita akan mendapatkan flag

```bash
┌──(naren㉿NarenTzy)-[~/ctf/picoCTF/foren/Weird File]
└─$ echo cGljb0NURnttNGNyMHNfcl9kNG5nM3IwdXN9 | base64 -d
picoCTF{m4cr0s_r_d4ng3r0us}  
```

`picoCTF{m4cr0s_r_d4ng3r0us}`