# Docker Starter Kit

Konfigurasi Docker siap pakai untuk pengguna XAMPP yang ingin pindah ke Docker.
Windows 10/11.

Polanya: **satu database dijalankan sekali**, dipakai bersama semua proyek lewat
network `devnet`. Tidak perlu membuat database baru untuk tiap proyek.

```
docker-starter-kit/
├── database/              MariaDB — jalankan ini lebih dulu
└── templates/
    ├── laravel/           Laravel
    ├── php-apache/        CodeIgniter 4 & PHP native
    ├── node/              Express, Next.js, NestJS
    └── python/            Django, FastAPI, Flask
```

Setiap folder punya README sendiri.

## Pemasangan Docker

**1. Cek virtualisasi.** Task Manager → Performance → CPU → `Virtualization: Enabled`.
Kalau `Disabled`, aktifkan **Intel VT-x** / **SVM Mode** di BIOS.

**2. Pasang WSL 2.** PowerShell sebagai Administrator:

```powershell
wsl --install
```

Restart laptop, lalu pastikan versinya 2:

```powershell
wsl --status
wsl --set-default-version 2
```

**3. Pasang Docker Desktop.** Unduh di https://www.docker.com/products/docker-desktop,
biarkan opsi *Use WSL 2* tercentang. Restart, buka aplikasinya, tunggu status
**Engine running**.

Docker Desktop harus terbuka setiap kali dipakai — seperti XAMPP Control Panel.

**4. Uji.**

```bash
docker --version
docker compose version
docker run --rm hello-world
```

Muncul `Hello from Docker!` berarti berhasil.

**5. Buat network bersama.** Cukup sekali, dipakai seluruh proyek:

```bash
docker network create devnet
docker network ls
```

## Urutan Pemakaian

1. Jalankan database → folder `database/`
2. Sambungkan ke Navicat: host `localhost`, port `3307`
3. Buat database untuk proyekmu lewat Navicat
4. Salin template yang sesuai ke folder proyek → folder `templates/`
5. `docker compose up -d --build`

## Istilah

| Istilah | Arti |
|---|---|
| Image | Cetakan aplikasi, read-only |
| Container | Image yang dijalankan |
| Volume | Penyimpanan data agar tidak hilang saat container dihapus |
| Network | Jalur komunikasi antar container |

## Dua Alamat Database

| Diakses dari | Host | Port |
|---|---|---|
| Navicat / aplikasi di Windows | `localhost` | `3307` |
| Container lain (Laravel, CI4, dll) | `db` | `3306` |

Container tidak mengenal `localhost` milik Windows. Ini penyebab error koneksi
yang paling sering terjadi.

## Perintah Harian

```bash
docker ps                 # container yang berjalan
docker images             # image tersimpan
docker logs -f <nama>     # lihat log
docker stop <nama>        # hentikan
docker start <nama>       # jalankan lagi
docker rm <nama>          # hapus container
```

Di dalam folder yang ada `docker-compose.yml`:

```bash
docker compose up -d --build   # jalankan
docker compose down            # hentikan & hapus container
docker compose down -v         # hapus container + data volume
docker compose logs -f         # lihat log
docker compose ps              # status
docker compose exec app bash   # masuk ke dalam container
```

## Masalah Umum

| Error | Solusi |
|---|---|
| `docker: command not found` | Docker belum terpasang, atau tutup lalu buka lagi terminal |
| `error during connect ... dockerDesktopLinuxEngine` | Docker Desktop belum dibuka |
| `WSL 2 installation is incomplete` | `wsl --update` di PowerShell Administrator |
| `network devnet not found` | `docker network create devnet` |
| `network devnet already exists` | Bukan error, network memang sudah ada |
| `port is already allocated` | Port dipakai aplikasi lain, ubah di `.env` |
| Docker Desktop tidak selesai *starting* | Restart; pastikan virtualisasi aktif di BIOS |
