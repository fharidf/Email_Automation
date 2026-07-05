# 📧 Email Automation

Aplikasi Python untuk mengirim email secara otomatis berdasarkan data dari file Excel.

---

## 📋 Deskripsi

Program ini membaca file Excel yang berisi daftar penerima email, lalu mengirimkan
email satu per satu secara otomatis. Setiap pengiriman dicatat statusnya (Terkirim/Gagal)
langsung di file Excel, disertai log lengkap di folder `logs/`.

**Fitur utama:**
- Baca data penerima dari file Excel (`.xlsx`)
- Kirim email dengan lampiran opsional
- Validasi format email sebelum pengiriman
- Catat status (`Terkirim` / `Gagal`) dan timestamp di Excel
- Logging lengkap ke file di folder `logs/`
- Program tidak berhenti meski satu email gagal
- Lewati otomatis baris yang sudah berstatus `Terkirim`

---

## 📁 Struktur Folder

```
Email_Automation/
│
├── main.py               # Entry point — jalankan file ini
├── config.py             # Konfigurasi terpusat (baca .env)
├── excel_reader.py       # Modul baca/tulis Excel
├── email_sender.py       # Modul koneksi SMTP dan kirim email
├── logger.py             # Konfigurasi sistem logging
├── utils.py              # Fungsi utilitas (validasi email, dll)
├── create_sample_excel.py # Script pembantu buat file Excel contoh
│
├── requirements.txt      # Daftar dependency Python
├── .env.example          # Template file konfigurasi
├── .env                  # ⚠️ File rahasia — JANGAN di-commit ke Git
├── .gitignore
├── README.md
│
├── data/
│   └── data_email.xlsx   # File Excel berisi daftar penerima
│
├── attachments/          # Simpan file lampiran di sini
│
└── logs/                 # Log otomatis tersimpan di sini
    └── email_automation_YYYY-MM-DD.log
```

---

## ⚙️ Cara Instalasi

### 1. Buat Virtual Environment

Virtual environment mengisolasi dependency project ini dari Python global.

```bash
# Windows
python -m venv venv
venv\Scripts\activate

# Linux / macOS
python3 -m venv venv
source venv/bin/activate
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

### 3. Siapkan File Konfigurasi

Salin file `.env.example` menjadi `.env`:

```bash
# Windows
copy .env.example .env

# Linux / macOS
cp .env.example .env
```

Lalu buka file `.env` dan isi dengan data Anda:

```env
EMAIL_ADDRESS=emailanda@gmail.com
EMAIL_PASSWORD=xxxx xxxx xxxx xxxx
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587
```

---

## 🔑 Cara Mendapatkan App Password Gmail

> ⚠️ Gmail tidak mengizinkan login langsung dengan password akun biasa.
> Anda harus menggunakan **App Password**.

1. Buka [Google Account](https://myaccount.google.com)
2. Pilih **Security** (Keamanan)
3. Aktifkan **2-Step Verification** jika belum aktif
4. Cari **App Passwords** → klik
5. Pilih app: **Mail**, pilih device: **Windows Computer**
6. Salin 16-digit password yang muncul
7. Tempel ke `EMAIL_PASSWORD` di file `.env` (boleh dengan atau tanpa spasi)

---

## 📊 Format File Excel

File Excel harus memiliki kolom berikut (nama harus **persis sama**):

| Kolom | Keterangan | Wajib Diisi? |
|---|---|---|
| `Nama` | Nama penerima | Ya |
| `Email` | Alamat email penerima | Ya |
| `Subject` | Subject email | Ya |
| `Message` | Isi pesan | Ya |
| `Attachment` | Nama file lampiran (contoh: `laporan.pdf`) | Tidak |
| `Status` | Diisi otomatis oleh program | Tidak |
| `Error` | Diisi otomatis jika gagal | Tidak |
| `Tanggal Kirim` | Diisi otomatis oleh program | Tidak |

**Contoh isi Excel:**

| Nama | Email | Subject | Message | Attachment | Status | Error | Tanggal Kirim |
|---|---|---|---|---|---|---|---|
| Andi | andi@gmail.com | Halo Andi | Selamat pagi, Andi! | | | | |
| Budi | budi@gmail.com | Laporan | Terlampir laporan. | laporan.pdf | | | |

> **Catatan:** Kolom `Status`, `Error`, dan `Tanggal Kirim` **tidak perlu diisi** —
> program akan mengisinya secara otomatis.

---

## 📎 Cara Menambahkan Lampiran

1. Salin file lampiran ke folder `attachments/`
2. Isi kolom `Attachment` di Excel dengan **nama file saja** (bukan path lengkap)

Contoh: jika file ada di `attachments/laporan.pdf`, isi kolom dengan `laporan.pdf`

---

## ▶️ Cara Menjalankan Program

```bash
# Pastikan virtual environment sudah aktif
# Pastikan file .env sudah diisi
# Pastikan data/data_email.xlsx sudah ada

python main.py
```

**Atau untuk setup cepat, buat file Excel contoh dulu:**

```bash
python create_sample_excel.py
python main.py
```

---

## 📝 Contoh Output

```
==================================================
2026-06-30 09:10:01 | INFO     | PROGRAM EMAIL AUTOMATION DIMULAI
2026-06-30 09:10:01 | INFO     | Membaca file Excel: data/data_email.xlsx
2026-06-30 09:10:02 | INFO     | Berhasil membaca 4 baris data
2026-06-30 09:10:02 | INFO     | Validasi kolom Excel: OK
2026-06-30 09:10:03 | INFO     | Menghubungkan ke SMTP server smtp.gmail.com:587...
2026-06-30 09:10:04 | INFO     | Berhasil login sebagai emailanda@gmail.com
2026-06-30 09:10:05 | SUCCESS  | BERHASIL | andi@gmail.com | Andi Saputra
2026-06-30 09:10:06 | WARNING  | VALIDASI GAGAL | email-tidak-valid | Format email tidak valid
2026-06-30 09:10:07 | SUCCESS  | BERHASIL | dian@gmail.com | Dian Pratama

==================================================
       RINGKASAN PENGIRIMAN EMAIL
==================================================
  ✅  Berhasil Terkirim : 2
  ❌  Gagal             : 1
  ⏭️   Dilewati          : 0
  📊  Total Diproses    : 3
==================================================
```

---

## 🐛 Cara Mengatasi Error Umum

### `ValueError: EMAIL_ADDRESS belum diisi`
→ Salin `.env.example` menjadi `.env` dan isi semua nilai.

### `SMTPAuthenticationError`
→ Gunakan **App Password** Gmail, bukan password akun biasa.
→ Pastikan 2-Step Verification sudah aktif di akun Google.

### `FileNotFoundError: File Excel tidak ditemukan`
→ Pastikan file ada di `data/data_email.xlsx`.
→ Jalankan `python create_sample_excel.py` untuk membuat file contoh.

### `ValueError: Kolom wajib tidak ditemukan`
→ Periksa nama kolom di Excel — harus persis sama (huruf besar/kecil).
→ Kolom yang diperlukan: `Nama, Email, Subject, Message, Attachment, Status, Error, Tanggal Kirim`

### `ConnectionError` / `SMTPConnectError`
→ Periksa koneksi internet.
→ Pastikan `SMTP_SERVER` dan `SMTP_PORT` di `.env` sudah benar.

---

## 🔒 Keamanan

- **JANGAN** pernah menyimpan password di source code.
- **JANGAN** pernah commit file `.env` ke Git.
- File `.gitignore` sudah dikonfigurasi untuk mengabaikan `.env`.
- Selalu gunakan App Password, bukan password akun utama.

---

## 🚀 Rencana Pengembangan

- [ ] Dukungan HTML Email
- [ ] CC dan BCC
- [ ] Retry otomatis untuk email yang gagal
- [ ] Scheduler (kirim terjadwal)
- [ ] GUI dengan Tkinter / CustomTkinter
- [ ] Progress bar
- [ ] Template email
- [ ] Multi SMTP support
- [ ] Dashboard statistik
