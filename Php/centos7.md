# Install PHP di server CentOS 7

Note: jika perintah memerluka akses root silakan tambahkan "sudo" diawal comand

## Sebelum menginstall PHP

Pertama, update lokal repository:

```html
yum -y update
```

Selanjutnya tambahkan script untuk repo epel dan yum-utils:

```html
yum install epel-release yum-utils
```

Tambahkan php untuk centos 7 :

```html
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
```

## Install PHP 7.4 di CentOS 7

Note: berlaku juga untuk versi yang lain, sesuaikan dengan versi php

Mulai dengan aktifkan repository Remi PHP 7.4:

```html
yum-config-manager --enable remi-php74
```

Install PHP 7.3 dan beberapa modul umum PHP:

```html
yum install php php-common php-opcache php-mcrypt php-cli php-gd php-curl php-mysqlnd php-mbstring php-xml
```

Untuk melihat apakah php sudah terinstal ketil perintah berikut :

```html
php -v
```

## Konfigurasi PHP

Jika Anda menggunakan Apache sebagai web server Anda, cukup restart layanan Apache menggunakan perintah berikut dan PHP sudah siap untuk digunakan.

```html
systemctl restart httpd
```

Tidak seperti Apache, Nginx tidak memiliki dukungan bawaan untuk memproses file PHP sehingga kita perlu menginstal aplikasi terpisah seperti PHP FPM yang akan menangani file PHP.

```html
sudo yum install php-fpm
```

Periksa konfigurasi di file /etc/php-fpm.d/www.conf, pastikan sesuai dengan pengaturan seperti di bawah :

```html
nano /etc/php-fpm.d/www.conf
```

Outputnya sesuaikan dengan kode berikut:

```html
...
user = nginx
...
group = nginx
...
listen = /run/php-fpm/www.sock
...
listen.owner = nginx
listen.group = nginx
```

Pastikan direktori /var/lib/php memiliki kepemilikan yang benar, Gunakan perintah chown berikut untuk mengubah kepemilikan direktori tersebut menjadi grup nginx:

```html
chown -R root:nginx /var/lib/php
```

Setelah Anda melakukan perubahan, aktifkan dan mulai layanan PHP FPM:

```html
systemctl enable php-fpm
systemctl start php-fpm
```

Selanjutnya, edit direktif virtual host Nginx dan tambahkan blok lokasi berikut sehingga Nginx dapat memproses file PHP:

```html
server {

    # . . . kode lain 
 # Copy dan paste kode berikut
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass unix:/run/php-fpm/www.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
# copy sampai di sini saja.
}
```

Agar konfigurasi baru berlaku, restart layanan Nginx:

```html
systemctl restart nginx
```
