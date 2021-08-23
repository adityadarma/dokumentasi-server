# Membuat database slave pada MYSQL

Server dalam contoh ini memiliki IP berikut:

```html
Server 1 (Master) IP : 192.168.121.19
Server 2 (Slave) IP : 192.168.121.14
```

Pasikan kedua server sudah terinstal MYSQL/MariaDB.
Jika belum bisa klik link instalasi berikut: \
[MYSQL](https://www.mysql.com/) \
[MariaDB](https://www.mariadb.com/)

## Konfigurasikan Master Server

Pertama, kita akan mengkonfigurasi server MySQL master dan membuat perubahan berikut:

* Atur server MySQL untuk listen pada private IP
* Tetapkan ID server yang unik
* Aktifkan binary logging

Untuk melakukannya buka file konfigurasi MySQL dan tambahkan baris berikut di bagian [mysqld]:

```html
sudo nano /etc/my.cnf
```

konfigurasi /etc/my.cnf untuk server master sebagai berikut :

```html
[mysqld]
bind-address = 192.168.121.19
server-id = 1
log_bin = mysql-bin
```

Setelah selesai, restart layanan MySQL agar perubahan diterapkan. \
Pada MYSQL :

```html
systemctl restart mysqld
```

Pada MariaDB :

```html
systemctl restart mariadb
```

Langkah selanjutnya adalah membuat pengguna replikasi baru. Masuk ke MySQL server sebagai root:

```html
mysql -u root -p
```

Dari dalam prompt MySQL, jalankan query SQL berikut yang akan membuat user replica dan memberikan hak akses REPLICATION SLAVE kepada user tersebut :

```html
CREATE USER 'user_slave'@'192.168.121.14' IDENTIFIED BY 'ganti_dengan_password';
GRANT REPLICATION SLAVE ON *.* TO 'user_slave'@'192.168.121.14';
FLUSH PRIVILEGES;
```

Atau dengan perintah

```html
GRANT REPLICATION SLAVE ON *.* TO 'user_slave'@'%' IDENTIFIED BY 'ganti_dengan_password';
```

* Jika ingin bisa dikases dengan banyak IP (banyak slave) maka ubah IP menjadi persen(%)
* Berikan akses replikasi untuk semua database pada ser yang tadi dibuat
* Pada 'GRANT REPLICATION SLAVE ON' tanda bintang(*) pertama menandakan nama_database dan yang kedua menandakan nama_table.

Pastikan untuk mengubah IP dengan alamat IP mesin slave Anda. Anda dapat memberi nama user dengan nama lain yang Anda inginkan.

Saat masih di dalam prompt MySQL, jalankan perintah berikut yang akan mencetak nama file dan posisi biner.

```html
SHOW MASTER STATUS;
```

Perhatikan pada nama file dan position. Anda akan membutuhkan nilai-nilai ini ketika mengkonfigurasi server slave. Nilai-nilai ini mungkin akan berbeda di server Anda.

## Konfigurasikan Server Slave

Seperti untuk server master di atas, kita juga akan membuat perubahan berikut ke server slave:

* Atur server MySQL untuk listen pada private IP
* Tetapkan ID server yang unik
* Aktifkan binary logging

Buka file konfigurasi MySQL dan edit baris berikut :

```html
sudo nano /etc/my.cnf
```

konfigurasi /etc/my.cnf untuk server master sebagai berikut :

```html
[mysqld]
bind-address = 192.168.121.14
server-id = 2
log_bin = mysql-bin
```

Setelah selesai, restart layanan MySQL agar perubahan diterapkan. \
Pada MYSQL :

```html
systemctl restart mysqld
```

Pada MariaDB :

```html
systemctl restart mariadb
```

Langkah selanjutnya adalah mengkonfigurasi parameter yang akan digunakan server slave untuk terhubung ke server master. Login ke shell MySQL:

```html
mysql -u root -p
```

Pertama, hentikan server slave threads dengan perintah :

```html
STOP SLAVE;
```

Jalankan query berikut yang akan mengatur slave untuk mereplikasi master:

```html
CHANGE MASTER TO 
MASTER_HOST='192.168.121.19',
MASTER_USER='user_slave',
MASTER_PASSWORD='password_slave',
MASTER_LOG_FILE='mysql-bin.000001',
MASTER_LOG_POS=1326;
```

Pastikan Anda menggunakan alamat IP, nama user, dan kata sandi yang benar. Nama dan posisi file log harus sama dengan nilai yang Anda peroleh dari server master.

Setelah selesai, jalankan threads untuk server slave.

```html
START SLAVE;
```
