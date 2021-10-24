# Memberikan akses di AWS

Note: Biasanya ini terjadi karena di block oleh SElinux yang mengakibatkan beberapa proses tidak berjalan

## 1. Memberikan akses baca tulis webserver

```html
chcon -R -t httpd_sys_content_rw_t /path/yang/akan/dikasi/akses
```

## 2. Memberikan akses jaringan keluar

```html
sudo setsebool -P httpd_can_network_connect 1
```

## 3. Memberikan akses untuk membuat folder vendor (composer)

```html
sudo chmod -R 775  /ROOT_OF_YOUR_APP/vendor/
sudo chown -R $USER:$USER /ROOT_OF_YOUR_APP/vendor/
```
