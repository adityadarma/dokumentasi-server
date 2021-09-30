# Cara install wilcard SSL Let’s Encrypt di CentOS

SSL ini akan diimplementasikan pada domain dan subdomain

## Install keperluan repository

Sebelum menginstal certbot, diperlukan repository tabahan untuk instalalsi

#### install epel repository

```html
yum install epel-release
```

#### Install yum utilities

```html
yum install yum-utils
```

## Install Certbot

Pilih salah satu sesuai web server yang Anda gunakan apakah Apache atau Nginx.

#### Ini untuk Apache
 
 ```html
yum install python2-certbot-apache
```

#### Yang ini untuk Nginx

 ```html
yum install python2-certbot-nginx
```

### Membuat wilcard sertifikat SSL Let’s Encrypt

Selanjutnya membuat sertifikat SSL Let’s Encrypt dengan perintah berikut, dengan asumsi domain sudah terhubung ke server/VPS.

```html
certbot certonly --manual -d *.domain.com -d domain.com --agree-tos --manual-public-ip-logging-ok --preferred-challenges dns-01 --server https://acme-v02.api.letsencrypt.org/directory
```
Jika ada permintaan untuk memasukkan email, silakan di masukkan email yang nantinya akan menerima email sertifikat

```html
Enter email address (used for urgent renewal and security notices) (Enter 'c' to cancel): contoh@domain.com
```

Jika ada konfirmasi untuk langganan berita dari EFF (Electronic Frontier Foundation). Silakan masukkan N kemudian tekan Enter

#### Membuat record TXT pada DNS

````html
Please deploy a DNS TXT record under the name
_acme-challenge.domain.com with the following value:

gt0ESCp91jE-1igKpM0gYOoJ_FDT7wFnL0mAOV3ijn4

Before continuing, verify the record is deployed.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Press Enter to Continue
````

Tekan Enter

Kemungkinan akan ada 2 DNS TXT record untuk _acme-challenge.domain.com

#### Selanjutnya setup TXT yang tdi diberikan di DNS Zone

Setelah yakin sudah deploy DNS dengan benar, baru tekan Enter. Tanda kesuksesan membuat SSL informasinya seperti ini

```html
IMPORTANT NOTES:
- Congratulations! Your certificate and chain have been saved at:
/etc/letsencrypt/live/domain.com/fullchain.pem
Your key file has been saved at:
/etc/letsencrypt/live/domain.com/privkey.pem
````

### Konfigurasi SSL di Apache dan Nginx

Berikut ini contoh konfigurasi SSL di Apache dan Nginx, diadaptasikan ke web server yang Anda gunakan.

#### Apache

```html
<VirtualHost *:443>
    ServerName domain.com
    ServerAlias www.domain.com
    ServerAdmin man@domain.com
    DocumentRoot /var/www/html

    <Directory /var/www/html>
    AllowOverride All
    SSLRequireSSL
    Options +Includes -Indexes +ExecCGI
    </Directory>

    SSLEngine on
    SSLVerifyClient none
    SSLCertificateFile /etc/letsencrypt/live/domain.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/domain.com/privkey.pem
    SSLCertificateChainFile /etc/letsencrypt/live/domain.com/fullchain.pem
</VirtualHost>
```

#### Nginx

```html
server {
    listen 443 ssl http2;
    server_name idnetter.com www.idnetter.com;
    root /var/www/html;
    index index.php index.html index.htm;
    access_log /var/log/nginx/idnetter.com.log combined;
    error_log /var/log/nginx/idnetter.com.error.log error;

    ssl on;
    ssl_certificate /etc/letsencrypt/live/idnetter.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/idnetter.com/privkey.pem;

    ....
    .. dan seterusnya
}
```
