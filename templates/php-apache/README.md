# Template PHP + Apache

Untuk CodeIgniter 4 dan PHP native. Versi PHP bisa diganti (7.4 sampai 8.3).
Database memakai MariaDB dari folder `database/`.

## Pemakaian

**1. Salin isi folder ini** ke folder root proyek.

**2. Buat `.env`.**

```bash
copy .env.docker.example .env
```

Isi sesuai jenis proyek:

| | CodeIgniter 4 | PHP native |
|---|---|---|
| `DOCROOT` | `/var/www/html/public` | `/var/www/html` |
| `PHP_VERSION` | `8.2` | `7.4` atau `8.2` |

**3. Buat database** lewat Navicat, lalu isi `DB_NAME` di `.env`.

**4. Jalankan.**

```bash
docker compose up -d --build
```

Buka http://localhost:8080

## Koneksi Database di Kode

Host database adalah `db`, bukan `localhost`.

**PHP native** — tulis begini supaya kode tetap bisa jalan di XAMPP maupun Docker:

```php
$host = getenv('DB_HOST') ?: 'localhost';
$user = getenv('DB_USER') ?: 'root';
$pass = getenv('DB_PASS') ?: '';
$name = getenv('DB_NAME') ?: 'devdb';

$conn = mysqli_connect($host, $user, $pass, $name);
if (!$conn) die('Koneksi gagal: ' . mysqli_connect_error());
```

**CodeIgniter 4** — tidak perlu diubah. Nilai `database.default.*` sudah dikirim
lewat `docker-compose.yml` dan otomatis dibaca CI4.

Kalau ingin mengatur manual, di file `.env` CI4:

```env
database.default.hostname = db
database.default.database = nama_database
database.default.username = root
database.default.password = root
database.default.DBDriver = MySQLi
```

## Composer & Spark

```bash
docker compose exec app composer install
docker compose exec app php spark migrate
docker compose exec app php spark make:controller Home
docker compose exec app bash
```

## Ganti Versi PHP

Ubah `PHP_VERSION` di `.env`, lalu build ulang:

```bash
docker compose up -d --build
```

Proyek lama yang butuh PHP 7.4 dan proyek baru dengan PHP 8.3 bisa jalan
bersamaan — cukup beda `APP_PORT`.

## Masalah Umum

| Error | Solusi |
|---|---|
| Halaman menampilkan daftar file | `DOCROOT` salah. CI4 harus `/var/www/html/public`. |
| `404 Not Found` di semua halaman selain beranda | mod_rewrite/`.htaccess`. Pastikan file `.htaccess` ikut tersalin. |
| `mysqli_connect(): php_network_getaddresses` | Host masih `localhost`, ganti jadi `db` |
| `port is already allocated` | Ubah `APP_PORT` di `.env` |
| CI4: `Unable to write to writable` | `docker compose exec app chmod -R 777 writable` |
| Perubahan `.env` tidak berpengaruh | `docker compose up -d --build` |
