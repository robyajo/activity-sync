---
name: release-notes
description: "ACTIVATE setiap kali selesai mengerjakan perubahan yang dilihat pengguna: menambah fitur, memperbaiki bug, mengubah perilaku, atau memperbaiki keamanan. Triggers on: selesai memperbaiki, tambah fitur, ubah perilaku, perbaikan bug, releases/, releases/README.md, changelog, catatan rilis, source_key, rilis otomatis. Menulis/memperbarui berkas rilis JSON harian di folder releases/. Do NOT activate untuk perubahan internal yang tidak terlihat pengguna (refactor, tes, format, komentar internal) atau saat hanya MEMBACA catatan rilis."
metadata:
    author: project
---

# Catatan Rilis (Changelog) — Aplikasi Ini

Setiap perubahan yang **dirasakan pengguna** harus tercatat di dalam folder
`releases/`. Berkas JSON ini menjadi catatan resmi riwayat rilis aplikasi,
dan bentuknya PERSIS yang diterima API portfolio saat dikirim
(lihat `../SKILL.md` — kirim isi berkas ini apa adanya + `project.name`).

---

## Kapan WAJIB Menulis/Memperbarui Berkas Rilis

| Perubahan                                                         |           Berkas rilis?           |
| :---------------------------------------------------------------- | :-------------------------------: |
| Fitur baru, halaman baru, kolom/tombol baru                       |              **Ya**               |
| Bug diperbaiki (termasuk keluhan pengguna/admin)                  |              **Ya**               |
| Perubahan perilaku yang dirasakan (alur, validasi, pesan error)   |              **Ya**               |
| Perbaikan keamanan & penambalan vulnerability                     | **Ya** (`important` / `security`) |
| Peningkatan performa & kecepatan halaman/API                      |      **Ya** (`improvement`)       |
| Refactor internal tanpa perubahan perilaku, format kode, komentar |               Tidak               |
| Perubahan setengah jadi di balik feature flag                     |       Tidak (tunggu aktif)        |

> **Prinsip Utama**: Tanyakan _"Apakah pengguna akan menyadari atau merasakan
> dampak perubahan ini?"_ Jika ya, catat ke berkas rilis!

---

## ⚡ ATURAN UTAMA: 1 Berkas JSON Per Hari (Konsolidasi Harian)

- **MAKSIMAL 1 BERKAS JSON PER HARI KERJA**:
    - Jangan memecah pembaruan, fitur, dan perbaikan pada hari yang sama ke
      dalam berkas-berkas berbeda.
    - **Langkah Kerja**:
        1. Periksa folder `releases/` untuk tanggal hari ini
           (`releases/YYYY-MM-DD-*.json`).
        2. **Jika SUDAH ADA**:
            - Buka berkas JSON tersebut.
            - Tambahkan poin perubahan ke dalam array `items: [...]`.
            - Perbarui `title` dan `body` agar merefleksikan ringkasan harian
              terpadu.
            - Naikkan `type` jika ada kategori yang lebih tinggi
              (`important` > `feature` > `fix` > `improvement`).
        3. **Jika BELUM ADA**:
            - Buat berkas baru: `releases/YYYY-MM-DD-nama-singkat.json`.

---

## Format JSON Dwibahasa

```json
{
    "date": "2026-09-22",
    "type": "improvement",
    "title": {
        "id": "Judul perubahan dalam Bahasa Indonesia",
        "en": "Title of the change in English"
    },
    "body": {
        "id": "Satu atau dua kalimat narasi ringkasan perubahan.",
        "en": "One or two sentences summarizing the changes."
    },
    "items": [
        {
            "type": "new",
            "id": "Deskripsi fitur baru yang dirasakan pengguna.",
            "en": "Description of the new feature experienced by users."
        },
        {
            "type": "fix",
            "id": "Deskripsi bug yang diperbaiki dan perilakunya sekarang.",
            "en": "Description of the fixed bug and its new behavior."
        }
    ]
}
```

### Pedoman Kolom

| Kolom    | Aturan                                                                                                                  |
| :------- | :---------------------------------------------------------------------------------------------------------------------- |
| `date`   | `YYYY-MM-DD`, tanggal kerja saat perubahan dibuat.                                                                      |
| `type`   | `feature` · `fix` · `improvement` · `important`.                                                                        |
| `status` | Bawaan `"published"`, atau `"draft"` jika masih disiapkan.                                                              |
| `title`  | Wajib diisi kedua bahasa (`id` dan `en`). Maksimal 80 karakter.                                                         |
| `body`   | Narasi 1–2 kalimat: apa yang berubah dan mengapa itu penting bagi pengguna.                                             |
| `items`  | Array item perubahan (1–10 item), mencakup `type` (`new`, `fix`, `improvement`, `security`, `removed`), `id`, dan `en`. |

---

## Cara Menulis Deskripsi (Bahasa Publik & Bebas Kebocoran Teknis)

Catatan rilis ini dibaca langsung oleh publik. **DILARANG KERAS** memuat nama
library, framework, arsitektur internal, nama middleware, nama package, atau
file kode sumber:

| ❌ Dilarang (Terlalu Teknis / Celah Bocor)                          | ✅ Wajib (Bahasa Pelayanan Publik)                                                                             |
| :------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------- |
| "Penggantian rate limiter dengan guard berlapis 120 req/menit"       | "Peningkatan Kecepatan & Perlindungan Sistem: Akses halaman lebih responsif dengan sistem pengamanan berlapis." |
| "Hapus pustaka X dan ganti dengan Textarea"                         | "Pembaruan Formulir Layanan: Pengisian formulir permohonan kini lebih ringan dan lancar di semua perangkat."   |
| "Migrasi framework 16.3.5 dan runtime 19.3.0 dengan bundler baru"   | "Peningkatan Kestabilan Sistem: Pembaruan menyeluruh agar aplikasi tetap andal, cepat, dan nyaman digunakan."   |
| "Optimasi query dan pengurangan overhead JSON"                      | "Peningkatan Kecepatan Akses: Halaman layanan terbuka lebih cepat tanpa jeda pemuatan."                         |
| "Pencegahan serangan CSRF dan session leakage"                      | "Perlindungan Keamanan Akun: Penguatan enkripsi sesi login untuk menjaga kerahasiaan data pribadi pengguna."   |

---

## Rujukan

- Lokasi berkas: `releases/` di proyek ini.
- Kirim ke portfolio: ikuti `../SKILL.md` langkah 3 (isi berkas ini dikirim
  apa adanya sebagai body POST).
- Rilis lama yang belum terkirim: ikuti `../backfill.md`.

## ⚡ Protokol Rangkaian (setiap selesai kerja)

1. Tulis/perbarui berkas rilis hari ini di `releases/` (aturan di atas).
2. Kirim ke portfolio lewat skill `activity-sync` (`../SKILL.md`), source
   otomatis dari `date` + slug judul.
3. Laporkan keduanya kepada pengguna.
