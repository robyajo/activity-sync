ACTIVITY_PROJECT_KEYS=pk_80e642a892d964362e180107bc8fb417:Proyek Publik Bersama

> **Pola kunci app lain**: berkas `.ACTIVITY_PROJECT_KEYS.example` di folder
> paket adalah contoh format. Di app tujuan, salin jadi `.ACTIVITY_PROJECT_KEYS`
> lalu isi kunci `X-project` app itu — AI membaca berkas tersebut sebagai
> sumber kuncinya; baris ini hanya contoh kunci bersama.

# Kontrak Route Activity Sync

Basis: `https://ajoroby.is-a.dev`. Semua respons JSON. Waktu dalam ISO-8601
dengan offset (contoh `2026-10-16T10:00:00+07:00`).

## POST /api/activities — kirim / revisi kegiatan

**Header wajib**

| Header         | Isi                                                                                     |
| -------------- | --------------------------------------------------------------------------------------- |
| `Content-Type` | `application/json`                                                                      |
| `X-project`    | kunci proyek (dibuat dari `/admin/projects`, atau terdaftar di `ACTIVITY_PROJECT_KEYS`) |

Kunci valid bila tercatat di database (dibuat via admin) ATAU ada di registry
env — jadi menambah proyek tidak butuh ubah env maupun rebuild portfolio.

**Body (JSON)**

| Field                   | Wajib                     | Batas / nilai                                              |
| ----------------------- | ------------------------- | ---------------------------------------------------------- |
| `kind`                  | tidak                     | `deploy` · `feature` · `fix` · `update` (default `update`) |
| `project.name`          | saat kontak pertama       | maks 256 karakter                                          |
| `project.url`           | tidak                     | maks 2048 karakter, boleh `""`                             |
| `project.description`   | tidak                     | maks 2000 karakter, boleh `""`                             |
| `source`                | tidak (sangat dianjurkan) | id stabil, mis. nama berkas rilis, maks 256 karakter       |
| `title.id` / `title.en` | ya                        | masing-masing 1–300 karakter                               |
| `body.id` / `body.en`   | tidak                     | masing-masing maks 2000 karakter, boleh `""`               |
| `url`                   | tidak                     | maks 2048 karakter, boleh `""`                             |
| `occurred_at`           | tidak                     | datetime ISO dengan offset (default: waktu terima)         |

**Semantik**

1. Kunci dicek ke registry. Proyek yang belum tercatat **didaftarkan otomatis**
   (nama dari `project.name`, fallback nama registry). Kiriman berikut
   memperbarui nama/url/deskripsi bila dikirim + `last_activity_at`.
2. Bila `source` sudah pernah dikirim dari kunci yang sama, baris **diperbarui**
   (bukan duplikat).
3. Nama proyek tampil diambil dari data server, bukan mentah dari pengirim.

**Respons**

| Status        | Arti                                                 | Contoh body                                                                          |
| ------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------ |
| `201`         | tersimpan baru (+ `createdProject` bila proyek baru) | `{"ok": true, "id": 7, "project": "Nama", "createdProject": true, "updated": false}` |
| `200`         | revisi `source` yang sama                            | `{"ok": true, "id": 7, "project": "Nama", "createdProject": false, "updated": true}` |
| `401`         | tanpa header `X-project`                             | `{"error": "Missing X-project header."}`                                             |
| `403`         | kunci salah                                          | `{"error": "Unknown project key."}`                                                  |
| `422`         | payload salah                                        | `{"error": "Invalid payload.", "issues": [...]}`                                     |
| `400` / `500` | JSON rusak / gagal simpan                            | `{"error": "..."}`                                                                   |

## PATCH /api/activities/[id] — ubah eksplisit

- Header `X-project` wajib; id harus milik kunci itu (kalau tidak: `404` tak
  ada / `403` milik kunci lain).
- Body parsial: `kind`, `title.{id,en}`, `body.{id,en}`, `url`, `occurred_at`.
  Minimal satu field, kalau tidak `400`.
- Respons: `{ok: true, id}`.

## DELETE /api/activities/[id] — hapus

- Header `X-project` wajib; hanya milik kunci itu (`404`/`403` seperti di atas).
- Respons: `{ok: true, id}`.

## GET /api/activities — daftar publik

- Query: `?limit=20&project=<kunci>` (`limit` 1–50, default 20; `project` opsional, filter by kunci).
- Tanpa auth. Respons: `{"ok": true, "total": N, "items": [...]}`.
- Tiap item: `id, projectKey, projectName, kind, titleId, titleEn, bodyId, bodyEn, url, occurredAt`.

## GET /api/projects — daftar proyek

- Query opsional: `?key=<kunci>` (harus valid, kalau tidak `403`) — hanya
  proyek berkunci itu, agar AI mempelajari proyek yang ada.
- Tanpa query: seluruh proyek publik.
- Tiap proyek: `name, url, description, activityCount, lastActivityAt`.
- **Kunci API tidak pernah muncul** di respons mana pun.

## GET /api/projects/[slug] — detail satu proyek

- `slug` cocok dengan slug halaman showcase atau nama proyek sync yang
  di-slugify. Untuk proyek sync, sertakan `?key=<kunci>` miliknya agar
  kegiatan ikut tampil (tanpa kunci: meta + catatan saja).
- Respons: `{ok, project, total, items}` atau `404`.
