# Install Firewall di Cent OS 7

Note: jika perintah memerluka akses root silakan tambahkan "sudo" diawal comand

## Instalasi

```html
yum install firewalld
```

Jalan perintah berikut untuk menjalankan service Firewall

```html
systemctl start firewalld
```

Silakan cek status service Firewall dengan perintah

```html
systemctl status firewalld
```

Untuk membuat service berjalan saat server malakukan restart atau booting ulang, jalankan perintah berikut

```html
systemctl enable firewalld
```

Mengaktifkan port server

```html
sudo firewall-cmd --zone=public --add-port=22/tcp --permanent
sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
sudo firewall-cmd --zone=public --add-port=443/tcp --permanent
sudo firewall-cmd --zone=public --add-port=3306/tcp --permanent
sudo firewall-cmd --reload
```
