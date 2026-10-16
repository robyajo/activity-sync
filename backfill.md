# Backfill rilis lama (OS-agnostik)

Dokumen ini untuk AI di OS mana pun (Linux / Windows / Mac): kirim ulang semua
berkas `releases/*.json` proyek ke API portfolio, sekali saja per proyek.
Setelah itu cukup kirim per rilis baru sesuai `SKILL.md`.

Prasyarat: `KEY` (kunci `X-project` proyek ini), `API`
(`https://ajoroby.is-a.dev/api/activities`), nama tampil proyek (`NAMA`).

## Langkah

1. **Daftar berkas**: baca direktori `releases/`, ambil semua `*.json`,
   urutkan abjad (terlama dulu bila namanya bertanggal).
2. **Untuk tiap berkas** (contoh `releases/2026-10-16-fitur-x.json`):
   - Baca JSON. Ambil `type`, `title.id`, `title.en`, `body.id`, `body.en`.
   - Petakan `type` → `kind`: `feature`→`feature`, `fix`→`fix`,
     `important`→`feature`, `improvement`→`update`, lainnya→`update`.
   - Lewati berkas bila `title.id` atau `title.en` kosong (catat sebagai skip).
   - Bila `body` kosong tapi ada `items`, gabungkan teks `items` (id dengan id,
     en dengan en) sebagai isi.
   - Potong: judul maks 300 karakter, isi maks 2000 karakter.
   - Bentuk payload:
     ```json
     {
       "kind": "<hasil-peta>",
       "project": { "name": "<NAMA>" },
       "source": "<nama-berkas saja, mis. 2026-10-16-fitur-x.json>",
       "title": { "id": "...", "en": "..." },
       "body": { "id": "...", "en": "..." }
     }
     ```
   - POST ke `API` dengan header `Content-Type: application/json` dan
     `X-project: <KEY>`. Catat `id` + `updated` dari respons.
   - Bila gagal (bukan 200/201), catat nama berkas + pesan error, lanjutkan
     ke berkas berikut. Beri jeda singkat antar kiriman.
3. **Verifikasi**: `GET /api/activities?limit=5` — jumlah baris harus sama
   dengan jumlah berkas terkirim (revisi menimpa, bukan dobel).
4. **Laporkan**: terkirim X, dilewati Y (sebutkan nama + alasan), gagal Z.

## Contoh perintah (sesuaikan shell masing-masing OS)

```bash
curl -X POST "https://ajoroby.is-a.dev/api/activities" \
  -H "Content-Type: application/json" \
  -H "X-project: <KEY>" \
  -d @payload.json
```

## Catatan

- `source` HARUS nama berkas (stabil) agar pengiriman ulang = update.
- Jangan kirim berkas yang bukan JSON valid atau tanpa judul dwibahasa.
- Kunci tidak boleh ditulis ke git / log publik.
