# Install Web Server Nginx di Cent OS 7

Note: jika perintah memerluka akses root silakan tambahkan "sudo" diawal comand

## Instalasi

Tambahkan Repository Nginx ke Server

```html
yum install epel-release
```

Install Nginx dengan Command:

```html
yum install nginx
```

Jalan perintah berikut untuk menjalankan service Nginx

```html
systemctl start nginx
```

Silakan cek status service Nginx dengan perintah

```html
systemctl status nginx
```

Untuk membuat service berjalan saat server malakukan restart atau booting ulang, jalankan perintah berikut

```html
systemctl enable nginx
```

Jika anda menjalankan firewall, jalankan perintah berikut untuk mengizinkan lalu lintas HTTP dan HTTPS:

```html
firewall-cmd --permanent --zone=public --add-service=http 
firewall-cmd --permanent --zone=public --add-service=https
firewall-cmd --reload
```

## Server Root dan Konfigurasi

### Default Server Root

Direktori root server default adalah `/usr/share/nginx/html`. File yang ditempatkan di sana akan disajikan di server web Anda. Lokasi ini ditentukan dalam file konfigurasi blok server default yang dikirimkan bersama Nginx, yang terletak di `/etc/nginx/conf.d/default.conf`.

### Server Block Configuration

Blok server tambahan apa pun, yang dikenal sebagai Host Virtual di Apache, dapat ditambahkan dengan membuat file konfigurasi baru di `/etc/nginx/conf.d`. File yang diakhiri dengan .conf di direktori itu akan dimuat saat Nginx dimulai.

### Nginx Global Configuration

File konfigurasi Nginx utama terletak di `/etc/nginx/nginx.conf`. Di sinilah Anda dapat mengubah pengaturan seperti pengguna yang menjalankan proses daemon Nginx, dan jumlah proses pekerja yang muncul saat Nginx berjalan, antara lain.
