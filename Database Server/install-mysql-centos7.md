# Install MYSQL pada server CentOS 7

Note: jika perintah memerluka akses root silakan tambahkan "sudo" diawal comand

## Install MYSQL

Pertama tambahkan repository MYSQL sebelum melakukan instalasi

* Versi 8.0

```html
yum localinstall https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm
```

* Versi 5.7

```html
yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
```

Selanjutnya install MYSQL

```html
yum install mysql-community-server
```

Jalan perintah berikut untuk menjalankan service MYSQL

```html
systemctl start mysql
```

Silakan cek status service MYSQL dengan perintah

```html
systemctl status mysql
```

Untuk membuat service berjalan saat server malakukan restart atau booting ulang, jalankan perintah berikut

```html
systemctl enable mysql
```

## Security MYSQL

Silakan jalankan perintah berikut untuk melakukan configurasi security

```html
mysql_secure_installation
```

Ketika pertamakali ditanyakan terkait password root, silakan tekan enter untuk melewati \
Selanjutnya silakan buat password root untuk databse MYSQL
