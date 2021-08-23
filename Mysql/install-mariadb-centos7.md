# Install Maria DB pada server CentOS 7

Note: jika perintah memerluka akses root silakan tambahkan "sudo" diawal comand

## Install MariaDB

Pertama jalankan perintah untuk meginstall MariaDB

* versi 5.5

```html
yum install mariadb-server
```

Jalan perintah berikut untuk menjalankan service MariaDB

```html
systemctl start mariadb
```

Silakan cek status service MariaDB dengan perintah

```html
systemctl status mariadb
```

Untuk membuat service berjalan saat server malakukan restart atau booting ulang, jalankan perintah berikut

```html
systemctl enable mariadb
```

## Security MariaDB

Silakan jalankan perintah berikut untuk melakukan configurasi security

```html
mysql_secure_installation
```

Ketika pertamakali ditanyakan terkait password root, silakan tekan enter untuk melewati \
Selanjutnya silakan buat password root untuk databse MariaDb
