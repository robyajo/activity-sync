# Backfill rilis lama (OS-agnostik)

Dokumen ini untuk AI di OS mana pun (Linux / Windows / Mac): kirim ulang semua
berkas `releases/*.json` proyek ke API portfolio, sekali saja per proyek.
Setelah itu cukup kirim per rilis baru sesuai `SKILL.md`.

Prasyarat: `KEY` (kunci `X-project` proyek ini), `API`
(`https://ajoroby.is-a.dev/api/activities`), nama tampil proyek (`NAMA`).

## Langkah

1. **Daftar berkas**: baca direktori `releases/`, ambil semua `*.json`,
   urutkan abjad (terlama dulu bila namanya bertanggal).
2. **Untuk tiap berkas** (contoh `releases/2026-05-13-verifikasi-keaslian-e-surat.json`):
   - Baca JSON-nya UTUH — tidak perlu memetakan field; API menerima bentuk
     berkas rilis persis (`date`, `type`, `title`, `body`, `items[]`).
   - Sisipkan `"project": {"name": "<NAMA>"}` (bila berkasnya sudah lalu
     disisipkan sebelumnya, tinggal pakai).
   - Lewati berkas bila `title.id` atau `title.en` kosong (catat sebagai skip).
   - POST ke `API` dengan header `Content-Type: application/json` dan
     `X-project: <KEY>`; cukup berjeda singkat antar kiriman.
   - Catat `id` + `updated` dari respons.
   - Bila gagal (bukan 200/201), catat nama berkas + pesan error, lanjutkan
     ke berkas berikutnya.
3. **Verifikasi**: `GET /api/activities?limit=5` — jumlah baris harus sama
   dengan jumlah berkas terkirim (revisi menimpa, bukan dobel).
4. **Laporkan**: terkirim X, dilewati Y (sebutkan nama + alasan), gagal Z.

## Contoh perintah (sesuaikan shell masing-masing OS)

```bash
curl -X POST "https://ajoroby.is-a.dev/api/activities" \
  -H "Content-Type: application/json" \
  -H "X-project: <KEY>" \
  -d "$(cat releases/2026-05-13-verifikasi-keaslian-e-surat.json)"
```

## Catatan

- `source` otomatis dari `date` + slug judul; pengiriman ulang berkas yang
  sama/revisinya = update, bukan dobel.
- Jangan kirim berkas yang bukan JSON valid atau tanpa judul dualingua.
- Kunci tidak boleh ditulis ke git / log publik.
