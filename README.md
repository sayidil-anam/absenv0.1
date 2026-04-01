# SimKuliah Auto Attendance Bot

> *"Mahasiswa yang hadir secara fisik itu biasa. Mahasiswa yang deploy bot buat hadir — itu baru luar biasa."*

<div align="center">

![Python](https://img.shields.io/badge/Python-3.12-blue?style=flat-square&logo=python)
![Selenium](https://img.shields.io/badge/Selenium-4.x-43B02A?style=flat-square&logo=selenium)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-Automated-2088FF?style=flat-square&logo=github-actions)
![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-USK_SimKuliah-red?style=flat-square)

Bot absensi otomatis untuk [simkuliah.usk.ac.id](https://simkuliah.usk.ac.id).  
Jalan sendiri via GitHub Actions. Gratis. Tanpa server.

[English](#english) · [Bahasa Indonesia](#bahasa-indonesia)

</div>

---

<a name="bahasa-indonesia"></a>
# Bahasa Indonesia

## Cara Kerja

1. GitHub Actions berjalan sesuai jadwal cron yang kamu set
2. Bot login ke SimKuliah — termasuk solve CAPTCHA otomatis
3. Klik tombol absen (dan konfirmasi)
4. Kirim notifikasi ke HP kamu via ntfy

```
GitHub Actions ──► absen.py ──► SimKuliah
                      │
                   ntfy.sh ──► HP kamu
```

## Setup (5 menit, serius)

### 1. Fork repo ini

Klik tombol **Fork** di pojok kanan atas. Selesai, repo sudah jadi milik kamu.

### 2. Set GitHub Secrets

Buka repo kamu → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**.

Tambahkan 3 secret berikut:

| Secret | Isi |
|--------|-----|
| `NPM` | NPM kamu |
| `PASSWORD` | Password SimKuliah kamu |
| `NTFY_TOPIC` | Nama topic ntfy pilihanmu (contoh: `budi-simkul`) |

> `NTFY_TOPIC` boleh dikosongkan kalau tidak mau notifikasi. Bot tetap jalan.

### 3. Setup notifikasi di HP (opsional tapi dianjurkan)

1. Install app **ntfy** → [Android](https://play.google.com/store/apps/details?id=io.heckel.ntfy) / [iOS](https://apps.apple.com/app/ntfy/id1625396347)
2. Subscribe ke 3 topic ini (ganti `budi-simkul` dengan topic milikmu):
   - `budi-simkul-bisa` — absen berhasil
   - `budi-simkul-info` — bot jalan, tidak ada tombol absen
   - `budi-simkul-gagal` — ada error

### 4. Sesuaikan jadwal kuliah

Edit `.github/workflows/absen.yml`. Cron berjalan dalam **UTC**, jadi WIB dikurangi 7 jam.

```
WIB 10:45  →  UTC 03:45  →  cron: '45 3 * * 2'
```

Contoh jadwal:

| Hari | WIB | Cron (UTC) |
|------|-----|------------|
| Selasa | 10:45 | `45 3 * * 2` |
| Rabu | 14:00 | `0 7 * * 3` |
| Rabu | 16:35 | `35 9 * * 3` |
| Kamis | 08:00 | `0 1 * * 4` |
| Kamis | 14:00 | `0 7 * * 4` |
| Sabtu | 14:00 | `0 7 * * 6` |

### 5. Update jadwal_cache.json

File `jadwal_cache.json` berisi daftar pertemuan per mata kuliah dengan tanggalnya. Bot pakai file ini untuk tahu apakah hari ini ada jadwal atau tidak.

Kalau kamu punya akses ke CLI tool-nya (`simkul`), jalankan `simkul jadwal --refresh` untuk update otomatis. Kalau tidak, edit manual atau minta temanmu yang sudah setup CLI untuk generate file-nya.

### 6. Test

Buka tab **Actions** → **Absensi Otomatis** → **Run workflow** → **Run workflow**.

Lihat log-nya. Kalau berhasil, notif masuk ke HP. Kalau gagal, cek log — biasanya CAPTCHA salah baca, coba run lagi.

---

## Penjelasan File

| File | Fungsi |
|------|--------|
| `absen.py` | Script utama — login, solve CAPTCHA, klik absen, kirim notif |
| `jadwal_cache.json` | Data jadwal pertemuan per MK (di-commit ke repo) |
| `.github/workflows/absen.yml` | Jadwal cron dan pipeline GitHub Actions |

---

## Development Lokal

```bash
# Clone repo
git clone https://github.com/NapoleonPro/absenv0.1.git
cd absenv0.1

# Install dependencies
pip install selenium ddddocr python-dotenv

# Buat file .env
echo "NPM=npm_kamu" > .env
echo "PASSWORD=password_kamu" >> .env
echo "NTFY_TOPIC=topic_kamu" >> .env

# Jalankan
python absen.py
```

> Butuh Google Chrome terinstall di lokal.

---

## Tech Stack

| Komponen | Kegunaan |
|----------|----------|
| Python 3.12 | Runtime |
| Selenium | Otomatisasi browser |
| ddddocr | Solve CAPTCHA |
| Chrome headless | Browser engine |
| python-dotenv | Baca `.env` lokal |
| GitHub Actions | Scheduler gratis |
| ntfy.sh | Push notification gratis |

---

## Disclaimer

Bot ini dibuat untuk keperluan pribadi. Developer tidak bertanggung jawab atas segala konsekuensi akademik akibat penggunaan bot ini. SimKuliah bisa update UI-nya kapan saja — kalau bot tiba-tiba tidak jalan, cek dulu apakah ada perubahan di sisi web-nya.

Selalu verifikasi absensimu tercatat di SimKuliah. Bot bisa salah baca CAPTCHA.

---

<a name="english"></a>
# English

## How It Works

1. GitHub Actions triggers on your configured cron schedule
2. Bot logs in to SimKuliah — including automatic CAPTCHA solving
3. Clicks the attendance button (and confirmation)
4. Sends a push notification to your phone via ntfy

## Setup

### 1. Fork this repository

Click **Fork** in the top right corner.

### 2. Set GitHub Secrets

Go to **Settings** → **Secrets and variables** → **Actions** → **New repository secret**.

| Secret | Value |
|--------|-------|
| `NPM` | Your student ID |
| `PASSWORD` | Your SimKuliah password |
| `NTFY_TOPIC` | Your chosen ntfy topic name (e.g. `budi-simkul`) |

### 3. Set up push notifications (optional)

1. Install **ntfy** → [Android](https://play.google.com/store/apps/details?id=io.heckel.ntfy) / [iOS](https://apps.apple.com/app/ntfy/id1625396347)
2. Subscribe to these 3 topics (replace `budi-simkul` with yours):
   - `budi-simkul-bisa` — attendance successful
   - `budi-simkul-info` — bot ran, no attendance button found
   - `budi-simkul-gagal` — error occurred

### 4. Adjust your schedule

Edit `.github/workflows/absen.yml`. GitHub Actions runs on **UTC** — subtract 7 hours from WIB.

### 5. Test it

Go to **Actions** → **Absensi Otomatis** → **Run workflow**.

---

## Disclaimer

This project is for personal use only. The developer is not responsible for any academic policy violations. Always verify your attendance was recorded on SimKuliah.

---

<div align="center">
Made with too much coffee by <a href="https://github.com/NapoleonPro">NapoleonPro</a> — kalau membantu, kasih ⭐ dong.
</div>
