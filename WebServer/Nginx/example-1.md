```html
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    server_name adityadarma.my.id www.adityadarma.my.id;

    include snippets/letsencrypt.conf;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl http2;
    server_name adityadarma.my.id www.adityadarma.my.id;

    root /var/www/html/adityadarma.my.id;
    index index.php index.html;

    access_log /var/log/nginx/adityadarma.my.id.access.log;
    error_log /var/log/nginx/adityadarma.my.id.error.log;

    ssl_certificate /etc/letsencrypt/live/adityadarma.my.id/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/adityadarma.my.id/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/adityadarma.my.id/chain.pem;
    include snippets/ssl.conf;
    include snippets/letsencrypt.conf;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location /angels {
        #alias /var/www/html/angels.adityadarma.my.id;
        #try_files $uri $uri/ @angels;

        #location ~ \.php$ {
        #    fastcgi_pass 127.0.0.1:9000;
        #    fastcgi_index index.php;
        #    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        #}

	    alias /var/www/html/angels.adityadarma.my.id;
        try_files $uri $uri/ /index.php$is_args$args;

        location ~ \.php$ {
            #fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
    }

    #location @angels {
    #    rewrite /angels/(.*)$ /angels/index.php?/$1 last;
    #}

    location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```
