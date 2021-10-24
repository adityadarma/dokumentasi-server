# Cara install SSL Let’s Encrypt di CentOS perdomain

SSL ini akan diimplementasikan pada domain dan subdomain dengan ssl yang berbeda

Note: jika perintah memerluka akses root silakan tambahkan "sudo" diawal comand

## Install keperluan repository

Sebelum menginstal certbot, diperlukan repository tabahan untuk instalalsi

### install epel repository

```html
yum install epel-release
```

#### Install yum utilities

```html
yum install yum-utils
```

## Install Certbot

Pilih salah satu sesuai web server yang Anda gunakan apakah Apache atau Nginx.

### Ini untuk Apache

```html
yum install certbot certbot-apache
```

### Yang ini untuk Nginx

 ```html
yum install certbot certbot-nginx
```

## Membuat sertifikat SSL Let’s Encrypt

Selanjutnya membuat sertifikat SSL Let’s Encrypt dengan perintah berikut, dengan asumsi domain sudah terhubung ke server/VPS.

### Apache

```html
certbot --apache -d example.com -d www.example.com
```

### Nginx

```html
certbot --nginx -d example.com -d www.example.com
```

Jika ada permintaan untuk memasukkan email, silakan di masukkan email yang nantinya akan menerima email sertifikat

```html
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator nginx, Installer nginx
Enter email address (used for urgent renewal and security notices)
(Enter 'c' to cancel): contoh@example.com
```

### Konfirmasi registrasi SSL

````html
Starting new HTTPS connection (1): acme-v02.api.letsencrypt.org

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server. Do you agree?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: y
````

Ketik "y"

### Electronic Frontier Foundation

Jika ada konfirmasi untuk langganan berita dari EFF (Electronic Frontier Foundation). Silakan masukkan N kemudian tekan Enter

````html
Would you be willing, once your first certificate is successfully issued, to
share your email address with the Electronic Frontier Foundation, a founding
partner of the Let's Encrypt project and the non-profit organization that
develops Certbot? We'd like to send you email about our work encrypting the web,
EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: n
````

Ketik "n"

### Informasi jika sudah selesai

Tanda kesuksesan membuat SSL informasinya seperti ini

```html
Account registered.
Requesting a certificate for devgp.site and www.devgp.site

Performing the following challenges:
http-01 challenge for devgp.site
http-01 challenge for www.devgp.site
Waiting for verification...
Cleaning up challenges
Deploying Certificate to VirtualHost /etc/nginx/conf.d/example.com.conf
Deploying Certificate to VirtualHost /etc/nginx/conf.d/example.com.conf
Redirecting all traffic on port 80 to ssl in /etc/nginx/conf.d/example.com.conf
Redirecting all traffic on port 80 to ssl in /etc/nginx/conf.d/example.com.conf

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Congratulations! You have successfully enabled https://devgp.site and
https://www.devgp.site
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/devgp.site/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/devgp.site/privkey.pem
   Your certificate will expire on 2022-01-22. To obtain a new or
   tweaked version of this certificate in the future, simply run
   certbot again with the "certonly" option. To non-interactively
   renew *all* of your certificates, run "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
````

### Konfigurasi SSL di Apache dan Nginx

Berikut ini contoh konfigurasi SSL di Apache dan Nginx, diadaptasikan ke web server yang Anda gunakan.

### Config Apache

```html
<VirtualHost *:443>
    ServerName example.com
    ServerAlias www.example.com
    ServerAdmin man@example.com
    DocumentRoot /var/www/html

    <Directory /var/www/html>
    AllowOverride All
    SSLRequireSSL
    Options +Includes -Indexes +ExecCGI
    </Directory>

    SSLEngine on
    SSLVerifyClient none
    SSLCertificateFile /etc/letsencrypt/live/example.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/example.com/privkey.pem
    SSLCertificateChainFile /etc/letsencrypt/live/example.com/fullchain.pem
</VirtualHost>
```

### Config Nginx

```html
server {
    listen 443 ssl http2;
    server_name example.com www.example.com;

    root /var/www/html;
    index index.php index.html index.htm;

    access_log /var/log/nginx/example.com.log combined;
    error_log /var/log/nginx/example.com.error.log error;

    ssl on;
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    ....
    .. dan seterusnya
}
```

### Memperbarui Sertifikat SSL Secara Otomatis

Sertifikat SSL dari Let’s Encrypt hanya berlaku selama 90 hari dan harus diperbarui secara berkala. Kita dapat menggunakan crontab atau cronjob untuk menjalankan perintah memperbarui sertifikat SSL secara otomatis.

Masuk modifikasi crontab

```html
crontab -e
```

Buat penjadwalan untuk certbot. Menjalankan perintah memperbarui sertifikat SSL setiap hari pada jam 01.00

0 1 * * * /usr/bin/certbot renew --dry-run
