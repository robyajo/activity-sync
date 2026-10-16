# Sinkron Kegiatan Antar-Proyek (Activity Sync)

Portfolio ini (`https://ajoroby.is-a.dev`) bisa menerima kabar kegiatan dari
proyek-proyek lain dan menampilkannya otomatis di **halaman detail tiap proyek**
(`/projects/<slug>`).

## Cara pakai di proyek lain (untuk AI)

1. Pemilik membuat proyek di `/admin/projects` (isi judul + slug) lalu menekan
   **Buat** pada kolom kunci sinkron dan **menyalin** hasilnya.
2. Salin folder skill ini ke proyek tersebut:
   `.agents/skills/activity-sync/` (cukup berkas `SKILL.md`).
3. Tempel kunci ke konfigurasi AI proyek itu, lalu AI mengirim tiap ada
   perubahan yang dirasakan pengguna (lihat `SKILL.md`).

## Kunci proyek (`X-project`) — dibuat dari admin, bukan manual

- Alur resmi: `/admin/projects` → tambah/ubah proyek → **Buat** → **salin**.
  Kunci per proyek showcase; kegiatan berkunci itu tampil **hanya di halaman
  detail proyek tersebut**.
- Format kunci `pk_` + 32 hex (boleh dibuat manual via `openssl rand -hex 16`
  bila perlu, mis. untuk proyek tanpa halaman showcase).
- Daftar kunci hidup di env portfolio: `ACTIVITY_PROJECT_KEYS` (lokal:
  `.env.local`, produksi: env Vercel) — hanya cadangan untuk proyek tanpa
  halaman showcase. Kunci dari admin otomatis valid tanpa sentuh env,
  sehingga menambah proyek tidak butuh rebuild portfolio.
- Kunci yang bocor: regenerate dari dialog proyek + redeploy — kiriman dengan
  kunci salah ditolak (`403`) tanpa menyentuh database.

## Data proyek & revisi (disimpan di database)

- Kiriman pertama dari suatu (kunci, nama) **otomatis mendaftarkan proyek**
  (nama dari `project.name`, fallback nama registry). Kiriman berikut dengan
  nama sama memperbarui progres: url/deskripsi bila dikirim + `last_activity_at`.
- Bila payload menyertakan `source` stabil (nama berkas rilis) dan `source`
  itu sudah pernah dikirim, baris kegiatan **diperbarui** (judul/isi/kind/
  waktu ikut baru) — tidak duplikat. Inilah jalur "AI merevisi rilis".
- Daftar proyek publik: `GET /api/projects` (nama, url, deskripsi, jumlah
  kegiatan, update terakhir — tanpa kunci).

## Alur data + CRUD penuh

```
AI proyek lain --POST/PATCH/DELETE--> portfolio --tayang--> detail proyek + JSON-LD
   header X-project: <kunci>                validasi zod + registry/kepemilikan
```

- Cek proyek sendiri: `GET /api/projects?key=<kunci>` (butuh kunci valid).
- Detail + kegiatan: `GET /api/projects/<slug>?key=<kunci>` atau
  `GET /api/activities?slug=<slug-proyek>`.
- Buat/revisi: `POST /api/activities` (`source` stabil = update).
- Ubah eksplisit: `PATCH /api/activities/<id>` (parsial, milik sendiri).
- Hapus: `DELETE /api/activities/<id>` (milik sendiri).
- Tanpa header: `401`. Kunci salah / milik kunci lain: `403`. Payload salah: `422` + rincian.
- Daftar publik: `GET /api/activities?limit=20&project=<kunci>` (tanpa auth).

## Batas

- Judul maks 300 karakter, isi maks 2000 karakter, masing-masing `id` + `en`.
- Maks 50 entri per permintaan daftar.
