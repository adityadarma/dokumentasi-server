# Merecovery chmod -R 777 pada CentOS

Note: Jika anda melakukan chmod -R 777, ini adalah cara mengembalikannya via mode pemulihan

## Masuk ke mode pemulihan dengan boot ke ISO installer

```html
Pilih Troubleshoot -> Rescue a CentOS System
```

Setelah kami mencapai lingkungan Rescue ketik "1" untuk melanjutkan (pilih no dengan nama 'Continue'). ketik kode berikut:

```html
chroot /mnt/sysimage
```

atau

```html
chroot /mnt
```

Ini adalah untuk memastikan Anda berada di chroot, prompt Anda akan berubah dari sh shell ke bash shell di server berbasis RHEL 7/8.

## Kembalikan Izin

Pastikan anda sudah dalam bash shell, lalu ketik perintah berikut:

```html
# rpm --setugids -a
# rpm --setperms -a
```

Opsi yang digunakan di sini adalah `--setugids -a`, `--setpermsdan -a`

`--setperms` Ini akan mengatur izin file dalam paket RPM.
`--setugids` Ini akan mengatur kepemilikan file oleh pengguna/grup dalam paket RPM yang diberikan.
`-a` Saat kami menggunakan -a atau –all itu diterapkan untuk semua paket RPM yang diinstal.

Setelah selesai, keluar dari chroot dan dari sh shell dengan menjalankan exit dua kali.

## Failed to initialize NSS library in chroot

Jika mengalami kendala `Failed to initialize NSS library in chroot`

```html
yum update nspr
error: Failed to initialize NSS library
There was a problem importing one of the Python modules
required to run yum. The error leading to this problem was:

   cannot import name ts

Please install a package which provides this module, or
verify that the module is installed correctly.

It's possible that the above module doesn't match the
current version of Python, which is:
2.7.5 (default, Apr 11 2018, 07:36:10) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-28)]

If you cannot solve this problem yourself, please go to 
the yum faq at:
  http://yum.baseurl.org/wiki/Faq
```

Masalahnya adalah chroot mencegah akses ke /dev/urandom (seperti yang ditentukan). Pembaruan yang diinstal agar berhasil membutuhkan bit acak tersebut untuk menginisialisasi GnuTLS. Coba perintah berikut untuk mengatasinya

```html
mount -o bind /dev dev/
```

ke chroot dan lanjutkan dengan pembaruan seperti biasa.

Atau jika Anda tidak ingin memasang seluruh direktori /dev, Anda dapat membuatnya sendiri!

```html
mknod -m 666 /dev/random c 1 8
mknod -m 666 /dev/urandom c 1 9
```

Atau lebih baik gunakan

```html
mount -o bind /dev/urandom dev/urandom
```

Coba kembalikan izin dengan cara mengulang perintah sebelumnya

## SSH dengan Izin yang Benar

Setelah server mem-boot ulang SSH dan memverifikasi izin. Jika file host ssh tidak memiliki izin yang tepat, kita akan mendapatkan kesalahan koneksi ditolak.

```html
ssh root@192.168.0.175 ssh: sambungkan ke host 192.168.0.175 port 22: Sambungan ditolak
```

Ini adalah izin yang diperlukan untuk konfigurasi dan kunci host ini. Dengan mengubah izin, kami baik-baik saja untuk masuk sekarang.

```html
# chmod 644 /etc/ssh/ssh_config
# chmod 600 /etc/ssh/sshd_config
# chmod 640 /etc/ssh/ssh_host_rsa_key /etc/ssh/ssh_host_ecdsa_key /etc/ssh/ssh_host_ed25519_key
# chmod 644 /etc/ssh/ssh_host_rsa_key.pub /etc/ssh/ssh_host_ecdsa_key.pub /etc/ssh/ssh_host_ed25519_key.pub
```

Silakan login kembali dengan akun ssh anda.

Itu saja, kami telah berhasil memulihkan izin untuk paket RPM yang diinstal dan memulihkan server. Jangan pernah menggunakan chmod 777 pada sistem file atau konfigurasi apa pun.
