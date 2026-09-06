# Template Python

Untuk Django, FastAPI, dan Flask. Database memakai MariaDB dari folder `database/`.

## Pemakaian

**1. Salin isi folder ini** ke folder root proyek (sejajar `manage.py` atau `main.py`).

**2. Pastikan ada `requirements.txt`.** Kalau belum:

```bash
pip freeze > requirements.txt
```

Tambahkan driver MySQL sesuai framework:

```
mysqlclient        # Django
PyMySQL            # FastAPI / Flask / SQLAlchemy
```

**3. Buat `.env`,** lalu pilih baris `APP_COMMAND` sesuai framework.

```bash
copy .env.docker.example .env
```

**4. Jalankan.**

```bash
docker compose up -d --build
docker compose logs -f
```

Buka http://localhost:8000

## Koneksi Database

Host `db`, port `3306`.

**Django** — `settings.py`:

```python
import os

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': os.getenv('DB_NAME', 'devdb'),
        'USER': os.getenv('DB_USER', 'root'),
        'PASSWORD': os.getenv('DB_PASS', 'root'),
        'HOST': os.getenv('DB_HOST', 'localhost'),
        'PORT': os.getenv('DB_PORT', '3306'),
        'OPTIONS': {'charset': 'utf8mb4'},
    }
}
```

**SQLAlchemy / FastAPI** — connection string:

```python
import os
url = (
    f"mysql+pymysql://{os.getenv('DB_USER','root')}:{os.getenv('DB_PASS','root')}"
    f"@{os.getenv('DB_HOST','localhost')}:3306/{os.getenv('DB_NAME','devdb')}"
)
```

## Perintah

```bash
docker compose exec app python manage.py migrate
docker compose exec app python manage.py createsuperuser
docker compose exec app python manage.py collectstatic
docker compose exec app pip install nama-paket
docker compose exec app bash
```

Setelah memasang paket baru, catat ke requirements lalu build ulang:

```bash
docker compose exec app pip freeze > requirements.txt
docker compose up -d --build
```

## Masalah Umum

| Error | Solusi |
|---|---|
| `ModuleNotFoundError` | Paket belum ada di `requirements.txt`, lalu `docker compose up -d --build` |
| `Can't connect to MySQL server on 'localhost'` | `HOST` masih `localhost`, ganti jadi `db` |
| `mysqlclient` gagal dipasang | Ganti ke `PyMySQL`, atau pastikan build tidak dilewati |
| `DisallowedHost` di Django | Tambahkan `ALLOWED_HOSTS = ['*']` saat development |
| Server jalan tapi tak bisa dibuka | Harus listen `0.0.0.0`, bukan `127.0.0.1` |
| Perubahan kode tidak terbaca | `docker compose restart app` |
