---
name: activity-sync
description: "ACTIVATE setiap kali selesai mengerjakan perubahan yang dilihat pengguna di proyek ini: tambah fitur, perbaikan bug, ubah perilaku, rilis, atau hapus fitur. Kelola kegiatan dwibahasa (id + en) di API portfolio lewat CRUD penuh: baca, kirim, ubah, hapus, verifikasi. Triggers on: selesai memperbaiki, tambah fitur, ubah perilaku, perbaikan bug, rilis, deploy, hapus fitur."
---

# Activity Sync — CRUD kegiatan ke portfolio

Aturan dan alur route lengkap agar AI di proyek mana pun bisa mengelola kegiatan
di portfolio (`https://ajoroby.is-a.dev`) secara sempurna: baca, kirim, ubah,
hapus, verifikasi. Bahasa: selalu dwibahasa `id` + `en`.

Konfigurasi proyek ini (wajib ada sebelum mulai):

- `API = https://ajoroby.is-a.dev/api/activities`
- `KEY` = kunci `X-project` proyek ini — **baca dari berkas
  `.ACTIVITY_PROJECT_KEYS`** di folder skill ini (baris
  `ACTIVITY_PROJECT_KEYS=<kunci>:<nama>`). Jika berkas itu belum ada,
  salin `.ACTIVITY_PROJECT_KEYS.example` menjadi `.ACTIVITY_PROJECT_KEYS`,
  lalu minta pemilik mengisi kunci app ini — setelah terisi jangan tanya lagi.
  Berkas kunci asli tidak boleh di-commit ke git.

Jika folder ini berisi `route.md`, konsultasikan detail batas/parameter di sana;
folder paket lengkap tersedia di repo `activity-sync`.

## 1. Cek proyek yang ada (pelajari dulu)

```bash
curl -s "https://ajoroby.is-a.dev/api/projects?key=<KEY>"
```

Respons: daftar proyek berkunci ini (`name, url, description, activityCount,
lastActivityAt`). Kunci tidak pernah muncul di respons. Kunci salah → `403`.

## 2. Lihat detail + kegiatan (tahu yang kurang / yang perlu diubah)

```bash
# lewat slug halaman showcase:
curl -s "https://ajoroby.is-a.dev/api/activities?slug=<slug-proyek>&limit=20"
# atau detail proyek sync:
curl -s "https://ajoroby.is-a.dev/api/projects/<slug-nama-proyek>?key=<KEY>"
```

Bandingkan dengan kondisi proyek saat ini: yang belum tercatat = perlu dikirim,
yang berubah = perlu diupdate, yang dihapus di proyek = perlu dihapus di sini.

## 3. Kirim / revisi rilis (CREATE + UPDATE)

Satu endpoint POST untuk keduanya. Wajib sertakan `source` stabil (nama berkas
rilis, mis. `releases/2026-10-16-fitur-x.json`):

```bash
curl -X POST "https://ajoroby.is-a.dev/api/activities" \
  -H "Content-Type: application/json" \
  -H "X-project: <KEY>" \
  -d '{
    "kind": "feature",
    "project": { "name": "Nama Proyek" },
    "source": "releases/2026-10-16-fitur-x.json",
    "title": { "id": "...", "en": "..." },
    "body": { "id": "...", "en": "..." }
  }'
```

- `kind`: `deploy` · `feature` · `fix` · `update`.
- Proyek baru otomatis terdaftar saat pertama mengirim.
- `source` yang sama = **UPDATE** (`200, "updated": true`), baru = **CREATE**
  (`201, "updated": false`). Jadi setiap revisi berkas rilis → kirim ulang
  dengan `source` sama, konten portfolio ikut mutakhir.
- Tanpa header → `401`. Kunci salah → `403`. Payload salah → `422` + rincian.

## 4. Ubah eksplisit per id (UPDATE)

Bila perlu mengubah langsung tanpa lewat berkas rilis:

```bash
curl -X PATCH "https://ajoroby.is-a.dev/api/activities/<ID>" \
  -H "Content-Type: application/json" \
  -H "X-project: <KEY>" \
  -d '{"title": {"id": "...", "en": "..."}, "kind": "fix"}'
```

Field parsial: `kind`, `title.{id,en}`, `body.{id,en}`, `url`, `occurred_at`.
Hanya pemilik kunci itu yang bisa mengubah (milik kunci lain → `403`).

## 5. Hapus kegiatan (DELETE)

Setiap menghapus fitur/konten di proyek, hapus juga jejaknya di sini.
Cari dulu id-nya lewat langkah 2, lalu:

```bash
curl -X DELETE "https://ajoroby.is-a.dev/api/activities/<ID>" \
  -H "X-project: <KEY>"
```

Respons: `{"ok": true, "id": <ID>}`. Sama: hanya pemilik kunci.

## 6. Verifikasi hasil (wajib tiap kirim/ubah/hapus)

```bash
curl -s "https://ajoroby.is-a.dev/api/activities?slug=<slug-proyek>&limit=3"
```

Pastikan: id benar, judul/isi sesuai revisi terakhir, jumlah baris tidak dobel
(kirim ulang `source` sama harus tetap 1 baris). Cek juga halaman detail
proyek di browser bila memungkinkan.

## 7. Backfill rilis lama (sekali saja per proyek)

Ikuti `backfill.md` dalam paket ini (OS-agnostik: Linux/Windows/Mac).

## Aturan bahasa (wajib, security-safe & citizen-facing)

- `id` dan `en` wajib, bukan terjemahan mesin mentah.
- DILARANG menyebut nama library/package/framework/database/file kode/mekanisme internal.
- Rumuskan sebagai kabar pelayanan yang ramah dan berfokus pada dampak pengguna.

Benar: "Perbaikan formulir pendaftaran yang macet / Fixed the stuck
registration form". Salah: "Perbaiki bug Zod rate limiter".
