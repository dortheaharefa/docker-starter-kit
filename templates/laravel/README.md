# Template Laravel

PHP 8.3 + Apache. Database memakai MariaDB dari folder `database/`.

Pastikan database sudah jalan dan network `devnet` sudah ada.

## Pemakaian

**1. Salin isi folder ini** (`Dockerfile`, `docker-compose.yml`, `.dockerignore`,
folder `docker/`) ke folder root proyek Laravel — sejajar dengan `artisan`.

**2. Buat database** lewat Navicat, misal `laravel_app`.

**3. Ubah `.env` Laravel** bagian database:

```env
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=laravel_app
DB_USERNAME=root
DB_PASSWORD=root
```

`DB_HOST` diisi `db` — nama service database, bukan `localhost`.

**4. Jalankan.**

```bash
docker compose up -d --build
docker compose exec app composer install
docker compose exec app php artisan key:generate
docker compose exec app php artisan migrate
docker compose exec app chmod -R 777 storage bootstrap/cache
```

Buka http://localhost:8000

## Vite / Frontend

```bash
docker compose --profile node up -d
docker compose logs -f node
```

Untuk build produksi:

```bash
docker compose run --rm node npm run build
```

## Perintah Artisan

Semua perintah dijalankan di dalam container, diawali `docker compose exec app`:

```bash
docker compose exec app php artisan migrate
docker compose exec app php artisan make:controller UserController
docker compose exec app php artisan tinker
docker compose exec app composer require nama/paket
```

Kalau malas mengetik panjang, masuk saja ke dalam container:

```bash
docker compose exec app bash
```

## Ganti Port

Kalau 8000 dipakai, buat file `.env` di folder proyek (atau tambahkan barisnya):

```env
APP_PORT=8001
```

Lalu `docker compose up -d`.

## Masalah Umum

| Error | Solusi |
|---|---|
| `SQLSTATE[HY000] [2002] php_network_getaddresses` | `DB_HOST` masih `127.0.0.1`, ganti jadi `db` |
| `network devnet not found` | `docker network create devnet` |
| `The stream or file ... could not be opened` | `docker compose exec app chmod -R 777 storage bootstrap/cache` |
| Halaman kosong / error 500 | `docker compose logs -f app` |
| `Class not found` setelah tambah file | `docker compose exec app composer dump-autoload` |
| Vite: `Failed to load resource :5173` | Jalankan service node, atau `npm run build` |
