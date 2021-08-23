# Manage User pada MYSQL/MariaDB

## Create a new Database

Membuat database jika belum ada.

```html
CREATE DATABASE nama_database;
```

Membuat database jika sudah ada.

```html
CREATE DATABASE IF NOT EXISTS nama_database;
```

## List Database

```html
SHOW DATABASES;
```

## Delete Database

Secara langsung tanpa melihat.

```html
DROP DATABASE nama_database;
```

Di cek lalu dihapus

```html
DROP DATABASE IF EXISTS nama_database;
```

## Create a new MySQL user account

Membuat user database

```html
CREATE USER 'database_user'@'localhost' IDENTIFIED BY 'user_password';
```
