# Menambahkan secure pada link akses website

Buat hash password terlebih dahulu dengan command:

```html
openssl passwd
```

nanti akan muncul output

```html
O5az.RSPzd.HE
```

silakan buat file

```html
sudo nano /etc/nginx/pma_pass
```

silakan masukkan username dan password tadi sudah di hasing dengan format`username:password_hasing`, seperti contoh berikut:
```html
username:O5az.RSPzd.HE
```
masukkan file tersebut kedalam konfigurasi agar ketika di akses akan muncul popup untuk memasukkan username dan password
```html
server {
    listen 80;
    listen [::]:80;

    server_name namadomain.com www.namadomain.com;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl http2;
    server_name namadomain.com www.namadomain.com;

    root /usr/share/nginx/html/phpMyAdmin;
    index index.php index.html;

    access_log /var/log/nginx/namadomain.com.access.log;
    error_log /var/log/nginx/namadomain.com.error.log;

    ssl_certificate /etc/letsencrypt/live/namadomain/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/namadomain/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/namadomain/chain.pem;

    location / {
        # all untuk memastikan ip dan login memenuhi, any memastikan salah satu memenuhi
        satisfy any;

        # ip yang disetujui untuk di akses
        allow 182.253.138.87/24;
        #allow 127.0.0.1;

        # disable semua yang tidak memenuhi
        deny  all;

        # menggunakan auth basic
        auth_basic "User Login";
        auth_basic_user_file /etc/nginx/pma_pass;

        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```