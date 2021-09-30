# Konfigurasi Nginx untuk Codeigneter 3

Note: ini konfigurasi dasar, setiap setingan pasti menghasikan output berbeda

## HTTP Only

Konfigurasi ini hanya menggunakan `http` saja tanpa direct ke `https`

```html
server {
    listen 80;
    server_name namadomain.com www.namadomain.com;

    root /var/www/html/namadomian.com;
    index index.php index.html;

    access_log /var/log/nginx/namadomian.com.access.log;
    error_log /var/log/nginx/namadomian.com.error.log;

    location / {
        # Cek apakah file atau directori index ada, jika tidak panggil index.php.
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

## HTTP dan HTTPS

Konfigurasi ini akan meredirect dari `http` menjadi `https`

```html
server {
    listen 80;
    server_name namadomain.com www.namadomain.com;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name namadomain.com www.namadomain.com;

    root /var/www/html/namadomain.com;
    index index.php index.html;

    access_log /var/log/nginx/namadomain.com.access.log;
    error_log /var/log/nginx/namadomain.com.error.log;

    ssl_certificate /etc/letsencrypt/live/namadomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/namadomain.com/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/namadomain.com/chain.pem;

    location / {
        # Cek apakah file atau directori index ada, jika tidak panggil index.php.
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

## Penambahan repo dalam folder domain

Suatu website bisa saja menaruh projek dalam projek utama karena masih dalam satu domain yang sama

```html
location /path {
    try_files $uri $uri/ /folder_path/index.php?$query_string;
}
````

Contoh:

```html
server {
    listen 80;
    server_name namadomain.com www.namadomain.com;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name namadomain.com www.namadomain.com;

    root /var/www/html/namadomain.com;
    index index.php index.html;

    access_log /var/log/nginx/namadomain.com.access.log;
    error_log /var/log/nginx/namadomain.com.error.log;

    ssl_certificate /etc/letsencrypt/live/namadomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/namadomain.com/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/namadomain.com/chain.pem;

    location / {
        # Cek apakah file atau directori index ada, jika tidak panggil index.php.
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    # tambahan project diluar projek utama
    location /path {
        try_files $uri $uri/ /folder_path/index.php?$query_string;
    }
}
```

## mengarahkan path dengan ip lain

```html
location /laravel {
    rewrite /laravel/?(.*)$ /$1 break;
    proxy_pass http://generic.laravel;
}
```

### tambahan konfigurasi

Disable orang mengakses .htaccess

```html
# Deny for accessing .htaccess files for Nginx
location ~ /\.ht {
    deny all;
}
```

Disable orang mengakses folder
```html
# Deny for accessing folder
location ~ ^/(assets|storage)/ {
    return 403;
}
```

### inclde konfigurasi global

```html
# GZIP. Comment out to disable.
include /etc/nginx/snippets/gzip.conf;

# Client-side caching.
include /etc/nginx/snippets/client-cache.conf;

# Forbid access to log, txt, php, htaccess.
include /etc/nginx/snippets/security.conf;
```

Membuat konfigurasi global pada `/etc/nginx/snippets/security-php.conf`

```html
# Read more in https://www.nginx.com/resources/wiki/start/topics/recipes/drupal/

location ~\.(txt|log|conf|ini|composer|lock|xls|ods|xlsx|doc|docx|csv)$ {
    deny all;
}

# Block access to "hidden" files and directories whose names begin with a
# period. This includes directories used by version control systems such
# as Subversion or Git to store control files.
location ~ (^|/)\. {
    deny all;
}
```

Membuat konfigurasi global pada `/etc/nginx/snippets/client-cache-php.conf`
```html
location = /robots.txt {
    access_log off;
    log_not_found off;
    allow all;
}
location ~* \.(css|gif|ico|jpeg|jpg|js|png|woff|woff2|ttf|ttc|otf|eot)(\?[0-9]+)?$ {
    access_log off;
    expires max;
    #expires 365d;
    log_not_found off;
}
```

Membuat konfigurasi global pada `/etc/nginx/snippets/fastcgi-php.conf`
```html
try_files $uri =404;
fastcgi_split_path_info ^(.+\.php)(/.+)$;
fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
fastcgi_index index.php;
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
include fastcgi_params;
```

Membuat konfigurasi global pada `/etc/nginx/snippets/gzip-php.conf`
```html
# https://tweaked.io/guide/nginx/ control to save CPU.
gzip on;

gzip_vary on;

# Don't compress anything that's already small and unlikely to shrink much
# if at all (the default is 20 bytes, which is bad as that usually leads to
# larger files after gzipping). Set to 10K.
gzip_min_length 10000;

# Compression level (1-9).
# 5 is a perfect compromise between size and cpu usage, offering about
# 75% reduction for most ascii files (almost identical to level 9).
gzip_comp_level 5;

gzip_proxied expired no-cache no-store private auth;
gzip_disable "MSIE [1-6]\.";

# Compress all output labeled with one of the following MIME-types. https://mattstauffer.co/blog/enabling-gzip-on-nginx-servers-including-laravel-forge
gzip_types
application/atom+xml
application/javascript
application/json
application/rss+xml
application/vnd.ms-fontobject
application/x-font-ttf
application/x-web-app-manifest+json
application/xhtml+xml
application/xml
font/opentype
image/svg+xml
image/x-icon
text/css
text/plain
text/x-component;
# text/html is always compressed by HttpGzipModule
```

Membuat konfigurasi global pada `/etc/nginx/snippets/long-requests.conf`

```html
add_header Long-Timeout true always;

# Wait for ~5 min.
fastcgi_read_timeout 300;
proxy_connect_timeout 300;
proxy_send_timeout 300;
proxy_read_timeout 300;
send_timeout 300;
```