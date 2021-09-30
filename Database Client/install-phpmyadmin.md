# Install PhpMyAdmin pada CentOS 7

Note: jika perintah memerluka akses root silakan tambahkan "sudo" diawal comand

## install PhpMyAdmin

Sebelum menginstall, tambahkan terlebih dahulu repository berikut

```html
yum install epel-release
```

Jika sudah selesai maka lanjutkan dengan menginstall

```html
yum install phpmyadmin
```

biasanya file data phpmyadmin akan berada pada path `/usr/share/nginx/html/phpMyAdmin`
## membuat virtual host / block server

Apache:
```html
```

Nginx:
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