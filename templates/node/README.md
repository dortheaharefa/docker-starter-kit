# Template Node.js

Untuk Express, Next.js, NestJS, dan sejenisnya. Database memakai MariaDB dari
folder `database/`.

## Pemakaian

**1. Salin isi folder ini** ke folder root proyek (sejajar `package.json`).

**2. Buat `.env`.**

```bash
copy .env.docker.example .env
```

Sesuaikan `APP_PORT` dengan port aplikasimu: Express biasanya `3000`,
Next.js `3000`, NestJS `3000`, Vite `5173`.

**3. Pastikan ada skrip `dev`** di `package.json`:

```json
"scripts": {
  "dev": "nodemon index.js"
}
```

Next.js sudah punya bawaan. Untuk Express polos, pasang `nodemon` sebagai
devDependency.

**4. Jalankan.**

```bash
docker compose up -d --build
docker compose logs -f
```

Buka http://localhost:3000

`npm install` berjalan otomatis setiap container dinyalakan, jadi tidak perlu
memasang Node.js di Windows sama sekali.

## Koneksi Database

Host `db`, port `3306`. Contoh dengan `mysql2`:

```js
const mysql = require('mysql2/promise');

const db = await mysql.createConnection({
  host: process.env.DB_HOST || 'localhost',
  port: process.env.DB_PORT || 3306,
  user: process.env.DB_USER || 'root',
  password: process.env.DB_PASS || '',
  database: process.env.DB_NAME || 'devdb',
});
```

Prisma / Sequelize / TypeORM memakai connection string:

```
mysql://root:root@db:3306/devdb
```

## Perintah

```bash
docker compose exec app npm install nama-paket
docker compose exec app npx prisma migrate dev
docker compose exec app sh
docker compose restart app
```

## Masalah Umum

| Error | Solusi |
|---|---|
| `Cannot find module` setelah install paket baru | `docker compose restart app` |
| Perubahan kode tidak terdeteksi | Sudah diatasi lewat polling; kalau masih, `docker compose restart app` |
| `EADDRINUSE` | Ubah `APP_PORT` di `.env` |
| Aplikasi jalan tapi tak bisa dibuka di browser | Server harus listen ke `0.0.0.0`, bukan `localhost` |
| `getaddrinfo ENOTFOUND db` | Database belum jalan, atau belum tergabung `devnet` |
| `node_modules` kacau setelah ganti versi Node | `docker compose down -v` lalu `up -d --build` |
