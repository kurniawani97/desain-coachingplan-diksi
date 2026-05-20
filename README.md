# DIKSI – Coaching Plan System

Aplikasi React + Vite untuk sistem coaching plan PKWT (Bank Indonesia – KMBSO).

> ⚠️ **Catatan Penting:** Aplikasi ini adalah **mockup / prototipe tampilan (UI demo)** dari DIKSI. Tujuannya hanya untuk memperlihatkan rancangan antarmuka dan alur tampilan — **bukan aplikasi produksi**. Tidak ada backend, autentikasi, maupun database nyata yang terhubung. Seluruh data yang ditampilkan bersifat statis / dummy (lihat `dashboard_data.json`) dan tidak merepresentasikan data sebenarnya.

## Prasyarat

Sebelum menjalankan project, pastikan sudah terinstall:

- **Node.js** versi 18 atau lebih baru (disarankan LTS terbaru) — [download di sini](https://nodejs.org/)
- **npm** (otomatis ikut saat install Node.js)
- **Git** (opsional, untuk clone repo)

Cek versi yang terinstall:

```bash
node -v
npm -v
```

## Cara Menjalankan di Lokal

Langkah-langkah berikut sama untuk **Windows** dan **macOS**. Pada Windows gunakan **PowerShell** atau **Command Prompt**, pada macOS gunakan **Terminal**.

### 1. Masuk ke folder project

**macOS:**
```bash
cd "/Users/<username>/Documents/Bank Indonesia/KMBSO - PKWT/coachingPlan-system"
```

**Windows (PowerShell / CMD):**
```bash
cd "C:\Users\<username>\Documents\Bank Indonesia\KMBSO - PKWT\coachingPlan-system"
```

> Catatan: gunakan tanda kutip karena path mengandung spasi.

### 2. Install dependencies

```bash
npm install
```

Perintah ini akan men-download semua package yang dibutuhkan ke folder `node_modules/`. Dijalankan satu kali saat pertama kali setup, atau setiap kali ada perubahan di `package.json`.

### 3. Jalankan development server

```bash
npm run dev
```

Setelah build selesai, akan muncul output seperti:

```
  VITE v5.x.x  ready in xxx ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
```

Buka URL `http://localhost:5173/` di browser (Chrome / Edge / Safari).

Vite mendukung **hot reload** — setiap perubahan di file `src/` otomatis ter-refresh di browser.

### 4. Stop server

Tekan `Ctrl + C` di terminal.

## Build untuk Production

Untuk membuat versi production (hasil build masuk ke folder `dist/`):

```bash
npm run build
```

Untuk preview hasil build secara lokal:

```bash
npm run preview
```

## Struktur Project

```
coachingPlan-system/
├── src/                    # Source code React
│   ├── main.jsx           # Entry point aplikasi
│   ├── app.tsx
│   └── coaching_plan_v2.jsx
├── dashboard_data.json    # Data dashboard
├── index.html             # HTML template Vite
├── vite.config.js         # Konfigurasi Vite
├── package.json           # Dependencies & scripts
└── DATA_STRUCTURE.md      # Dokumentasi struktur data
```

## Troubleshooting

**`npm: command not found` / `'npm' is not recognized`**
Node.js belum terinstall atau belum masuk PATH. Install ulang dari [nodejs.org](https://nodejs.org/) lalu restart terminal.

**Port 5173 sudah dipakai**
Jalankan dengan port lain:
```bash
npm run dev -- --port 3000
```

**Error saat `npm install` (permission / EACCES di macOS)**
Hindari pakai `sudo`. Pastikan owner folder sesuai user:
```bash
sudo chown -R $(whoami) .
```

**Error `node_modules` corrupt**
Hapus folder lalu install ulang:

macOS:
```bash
rm -rf node_modules package-lock.json
npm install
```

Windows (PowerShell):
```powershell
Remove-Item -Recurse -Force node_modules, package-lock.json
npm install
```

**Akses dari device lain di jaringan yang sama**
```bash
npm run dev -- --host
```
Lalu buka alamat Network yang muncul di output.

## Script yang Tersedia

| Perintah          | Fungsi                                          |
| ----------------- | ----------------------------------------------- |
| `npm install`     | Install semua dependencies                      |
| `npm run dev`     | Jalankan development server (default port 5173) |
| `npm run build`   | Build production ke folder `dist/`              |
| `npm run preview` | Preview hasil build production                  |
