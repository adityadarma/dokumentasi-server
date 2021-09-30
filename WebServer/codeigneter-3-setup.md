Codeigniter 3 server configuration for Nginx & Apache
=====================================================

Web Server Site Configuration
-----------------------------

### Recommended Apache Configuration

Use the following configuration in Apache's httpd.conf file or within a virtual host configuration. Note that you should set `DocumentRoot` and `ServerName` fit to your environment:

```apache
<VirtualHost *:80>
    DocumentRoot "/var/www/codeignitor"
    ServerName www.example.com

    # Other directives here
</VirtualHost>
```

To enable pretty URL, add `.htaccess` in the webroot directory:

```apache
RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php/$1 [L]
```

### Recommended Nginx Configuration

To use Nginx, you should install PHP as an FPM SAPI. You may use the following Nginx configuration, replacing `root` value with the actual path for Codeignitor porject and `server_name` with the actual hostname to serve.

```nginx
server {
        server_name domain.tld;

        root /var/www/codeignitor;
        index index.html index.php;

        # set expiration of assets to MAX for caching
        #location ~* \.(ico|css|js|gif|jpe?g|png)(\?[0-9]+)?$ {
        #        expires max;
        #        log_not_found off;
        #}

        location / {
                # Check if a file or directory index file exists, else route it to index.php.
                try_files $uri $uri/ /index.php;
        }

        location ~* \.php$ {
                fastcgi_pass 127.0.0.1:9000;
		fastcgi_pass unix:/var/run/php5-fpm.sock;
                include fastcgi.conf;
		#fastcgi_param CI_ENV 'production';
        }
	
	# Deny for accessing .htaccess files for Nginx
	location ~ /\.ht {
            deny all;
        }
	
	# Deny for accessing codes
        location ~ ^/(application|system|tests)/ {
            return 403;
        }
}
```

Sub Directory Site Application
------------------------------

Codeiniter would guess your environment URI to implement pretty URL, if your Codeiniter application is under a sub folder of webroot, you just need to set the web server try file to current directory.

The following example would use `/_projects/codeigniter` as subdirectory path.

### Application BaseUrl

From the orignal Codeigniter config file `application/config/config.php`:

```php
$config['base_url'] = '';
$config['index_page'] = 'index.php';
```

We could set subdirectory path to `base_url` and disable `index_page`:

```php
$config['base_url'] = '/_projects/codeigniter';
$config['index_page'] = '';
```

After setting, you would able to consistently bind URL when you use Url generator:

```php
$this->load->helper('url');
echo site_url('controller/action'); 	// `/_projects/codeigniter/controller/action`
echo base_url("controller/action");	// `/_projects/codeigniter/controller/action`	
redirect('controller/action');		// Go to `/_projects/codeigniter/controller/action`
```


### Apache Configuration

It's easy to set pretty URL for Codeiniter in Apache where ever the application directory is, add `.htaccess` in the webroot directory:

```apache
RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php/$1 [L]
```

### Nginx Configuration

Customize the location path to current Codeiniter application directory:

```nginx
location /_projects/codeigniter/ {

	try_files $uri $uri/ /_projects/codeigniter/index.php;
}
```