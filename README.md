# Penilaian 360° — versi GitHub Pages (frontend statis + database Google Sheets)

Ini adalah versi web **normal**: file statis (`index.html` + `config.js`) yang bisa
di-hosting di GitHub Pages, Netlify, Vercel, atau server web apa pun — tidak lagi
harus dibuka lewat URL Apps Script. Database tetap Google Sheets yang sama; Apps
Script bertindak sebagai API di belakang layar.

```
Browser (index.html di GitHub Pages)
      │  fetch() ke /exec URL
      ▼
Apps Script Web App (Code.gs — TIDAK BERUBAH, dipasang seperti sebelumnya)
      │
      ▼
Google Sheets (Pegawai · Penilaian · Config)
```

## Yang Anda butuhkan

- Apps Script + Google Sheets yang **sudah Anda pasang sebelumnya** (paket
  `penilaian-360/Code.gs` + `Index.html`). Tidak perlu diubah atau di-deploy ulang.
- Sebuah repo GitHub kosong.

## Langkah 1 — ambil URL Web App

1. Buka proyek Apps Script Anda.
2. **Deploy → Manage deployments**.
3. Salin **Web app URL** — harus diakhiri `/exec`, contoh:
   `https://script.google.com/macros/s/AKfycb.../exec`
4. Pastikan *Who has access* diset **Anyone** (atau **Anyone with a Google account**
   bila Anda ingin email penilai ikut tercatat). Tanpa ini, GitHub Pages tidak bisa
   memanggil API-nya sama sekali.

## Langkah 2 — isi config.js

Buka `config.js` di paket ini, ganti baris:

```js
var APPS_SCRIPT_URL = 'GANTI_DENGAN_URL_WEB_APP_ANDA';
```

menjadi URL yang tadi disalin, misalnya:

```js
var APPS_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycb.../exec';
```

## Langkah 3 — upload ke GitHub

Lewat web (tanpa command line):

1. Buat repo baru di GitHub, misalnya `penilaian-360`.
2. **Add file → Upload files**, seret ketiga file di paket ini
   (`index.html`, `config.js`, `.nojekyll`).
3. Commit.

Atau lewat command line:

```bash
cd penilaian-360-github
git init
git add .
git commit -m "Penilaian 360 - frontend statis"
git branch -M main
git remote add origin https://github.com/USERNAME/penilaian-360.git
git push -u origin main
```

## Langkah 4 — nyalakan GitHub Pages

1. Di repo GitHub: **Settings → Pages**.
2. **Source**: `Deploy from a branch`.
3. **Branch**: `main`, folder `/ (root)`. Save.
4. Tunggu 1–2 menit, URL situs muncul di bagian atas halaman yang sama, bentuknya:
   `https://USERNAME.github.io/penilaian-360/`

Bagikan URL itu ke para penilai. Selesai — aplikasi jalan normal seperti web pada
umumnya, tanpa pop-up izin Google atau tampilan Apps Script.

## Kalau data tidak muncul / error saat memuat

Urutan pemeriksaan:

1. **`config.js` sudah diisi?** — Buka file itu di repo, pastikan bukan lagi
   `GANTI_DENGAN_URL_WEB_APP_ANDA`.
2. **URL berakhiran `/exec`, bukan `/dev`?** — URL `/dev` hanya bisa diakses oleh
   Anda sendiri yang login, bukan publik.
3. **Akses deployment "Anyone"?** — Cek lagi di Manage deployments.
4. **Versi deployment terbaru?** — Setiap Anda mengubah `Code.gs`, tetap harus
   **Deploy → Manage deployments → Edit → Version: New version**.
5. Buka Console browser (F12 → Console) di halaman GitHub Pages, lihat pesan galat
   persisnya — biasanya langsung menunjuk ke salah satu poin di atas.

## Kenapa ini bisa berjalan tanpa server sendiri

Permintaan `GET` (memuat data, melihat hasil) berjalan tanpa hambatan lintas domain.
Permintaan `POST` (mengirim penilaian, menambah pegawai) dikirim dengan header
`Content-Type: text/plain` alih-alih `application/json` — trik standar untuk Apps
Script, supaya browser tidak mengirim *preflight* `OPTIONS` yang tidak didukung
Apps Script. Apps Script sendiri tetap membaca isinya sebagai JSON seperti biasa,
jadi tidak ada perubahan apa pun di sisi `Code.gs` atau struktur Google Sheets.

## Dua versi yang kini Anda miliki

| | Versi Apps Script (paket lama) | Versi GitHub Pages (paket ini) |
|---|---|---|
| Frontend disajikan oleh | Apps Script (`Index.html`) | GitHub Pages / hosting statis apa pun |
| Cara frontend bicara ke backend | `google.script.run` | `fetch()` ke URL `/exec` |
| Database | Google Sheets | Google Sheets — **sama persis** |
| Cocok untuk | Cepat, tanpa perlu akun GitHub | Domain sendiri, tampil sebagai web biasa |

Keduanya boleh dipakai bersamaan — sama-sama membaca/menulis ke spreadsheet yang sama.
