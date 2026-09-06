# Database — MariaDB

Database dijalankan sekali, dipakai bersama semua proyek lewat network `devnet`.

## Menjalankan

```bash
copy .env.example .env
docker compose up -d
docker compose ps
```

Status harus `healthy`. Kalau belum, tunggu ±30 detik (inisialisasi pertama).

## Dua Alamat Berbeda

Ini yang paling sering keliru:

| Diakses dari | Host | Port |
|---|---|---|
| Navicat / aplikasi di Windows | `localhost` | `3307` |
| Container lain (Laravel, CI4, dll) | `db` | `3306` |

Container tidak mengenal `localhost` milik Windows. Antar container, alamatnya
adalah **nama service** — di sini `db`.

## Koneksi Navicat

Connection → MariaDB:

```
Host      : localhost
Port      : 3307
User      : root
Password  : root        (sesuai .env)
```

Bisa juga pakai user biasa: `dev` / `dev`.

## Import Database Lama

Export dulu dari phpMyAdmin XAMPP jadi `.sql`, lalu:

**Cara 1 — Navicat.** Klik kanan database → *Execute SQL File* → pilih file `.sql`.

**Cara 2 — Otomatis saat pertama kali.** Taruh file `.sql` di folder `initdb/`
sebelum `docker compose up -d`. Hanya berjalan kalau database masih kosong.

**Cara 3 — Terminal.**

```bash
docker exec -i devnet-db mariadb -uroot -proot devdb < backup.sql
```

## Membuat Database Baru

Tiap proyek sebaiknya punya database sendiri. Lewat Navicat: klik kanan koneksi
→ *New Database* → collation `utf8mb4_unicode_ci`.

Atau lewat terminal:

```bash
docker exec -it devnet-db mariadb -uroot -proot -e "CREATE DATABASE nama_proyek CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"
```

## Perintah

```bash
docker compose up -d      # jalankan
docker compose stop       # hentikan, data aman
docker compose down       # hapus container, data aman
docker compose down -v    # hapus container + SELURUH DATA
docker compose logs -f    # lihat log
docker exec -it devnet-db mariadb -uroot -proot   # masuk ke shell SQL
```

## Backup

```bash
docker exec devnet-db mariadb-dump -uroot -proot --all-databases > backup.sql
```

## Masalah Umum

| Error | Solusi |
|---|---|
| `port is already allocated` | Port 3307 dipakai. Ubah `DB_PORT_HOST` di `.env`. |
| `network devnet not found` | Jalankan `docker network create devnet` |
| Navicat: `Can't connect to server` | Container belum `healthy`, cek `docker compose ps` |
| Aplikasi: `getaddrinfo failed: db` | Container aplikasi belum tergabung di network `devnet` |
| `Access denied for user` | Password tidak cocok dengan `.env`. Ganti password lalu `docker compose down -v` dan `up -d` lagi. |
| File `.sql` di `initdb/` tidak terbaca | Hanya jalan saat data kosong. `docker compose down -v` lalu `up -d`. |
