# Aturan Activity Sync (tempel ke AGENTS.md proyek lain)

Salin seluruh blok di bawah ini ke `AGENTS.md` proyek tujuan (di bawah aturan
yang sudah ada). Ganti `<NAMA_PROYEK>` dengan nama tampil proyek itu.

```md
## Activity Sync — lapor kegiatan ke portfolio

Setiap selesai mengerjakan perubahan yang **dirasakan pengguna** (fitur baru,
perbaikan bug, perubahan perilaku, rilis, deploy, hapus fitur), AI WAJIB
mencatat di berkas rilis lalu mengirim satu entri kegiatan ke portfolio
sebelum menyelesaikan tugas. Refactor internal, format, komentar, dan tes yang
tak terlihat pengguna: jangan kirim.

1. Tulis/perbarui berkas rilis `releases/YYYY-MM-DD-*.json` memakai skill
   `release-notes` (folder `release-notes/` dalam paket activity-sync):
   konsolidasi 1 berkas JSON per hari, dwibahasa (id + en), bahasa pelayanan
   publik — bebas istilah teknis/library/framework.
2. Muat skill `activity-sync` (`.agents/skills/activity-sync/SKILL.md`) dan
   ikuti alur CRUD di sana: cek proyek (`GET /api/projects?key=`), kirim
   isi berkas rilis `releases/*.json` apa adanya lewat `POST` (tambah
   `"project": {"name": "…"}`; API yang memetakan `type`/`date`/`items`
   otomatis), ubah (`PATCH`), hapus (`DELETE`), lalu verifikasi (`GET` +
   cek tidak dobel).
3. Kunci `X-project` proyek ini: baca dari berkas `.ACTIVITY_PROJECT_KEYS`
   di folder skill ini (contoh format lihat `.ACTIVITY_PROJECT_KEYS.example`;
   salin jadi `.ACTIVITY_PROJECT_KEYS` lalu isi kunci app ini) — JANGAN commit
   berkas kunci ke git, JANGAN tampilkan ke user kecuali diminta.
4. Setiap kiriman wajib dwibahasa (`id` + `en`), maksimal 300 karakter judul /
   2000 karakter isi gabungan `body` + seluruh `items[]` per bahasa, dengan bahasa
   pelayanan publik (dilarang menyebut library, framework, database, file kode,
   atau mekanisme internal). Semua rincian harus dikirim; API menolak total yang
   melebihi batas, bukan memotong isinya diam-diam.
5. Untuk rilis lama yang belum terkirim: ikuti `backfill.md` (sekali saja).
6. Laporkan ke user: apa yang dikirim (id baris), hasil verifikasi, dan apa
   pun yang gagal.
```

Aturan tambahan untuk AI (`AGENTS.md` ini):

- Kunci di `.ACTIVITY_PROJECT_KEYS` adalah sumber kebenaran app ini (contoh
  format: `.ACTIVITY_PROJECT_KEYS.example`; kunci bersama portfolio ada di
  baris contoh itu). Bila respons API `401` (tanpa header) atau `403`
  (kunci salah), berhenti dan minta kunci baru ke pemilik — jangan menebak.
- `source` otomatis dari `date` + slug judul berkas rilis; bila mengirim
  bentuk kanonik, `source` HARUS stabil per rilis agar revisi menimpa baris
  yang sama.
