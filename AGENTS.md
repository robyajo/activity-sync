# Aturan Activity Sync (tempel ke AGENTS.md proyek lain)

Salin seluruh blok di bawah ini ke `AGENTS.md` proyek tujuan (di bawah aturan
yang sudah ada). Ganti `<NAMA_PROYEK>` dengan nama tampil proyek itu.

```md
## Activity Sync — lapor kegiatan ke portfolio

Setiap selesai mengerjakan perubahan yang **dirasakan pengguna** (fitur baru,
perbaikan bug, perubahan perilaku, rilis, deploy, hapus fitur), AI WAJIB
mengirim satu entri kegiatan ke portfolio sebelum menyelesaikan tugas.
Refactor internal, format, komentar, dan tes yang tak terlihat pengguna:
jangan kirim.

1. Muat skill `activity-sync` (`.agents/skills/activity-sync/SKILL.md`) dan
   ikuti alur CRUD di sana: cek proyek (`GET /api/projects?key=`), kirim/
   revisi (`POST` dengan `source` = nama berkas rilis), ubah (`PATCH`),
   hapus (`DELETE`), lalu verifikasi (`GET` + cek tidak dobel).
2. Kunci `X-project` proyek ini: baca dari baris 2 `route.md`
   (`ACTIVITY_PROJECT_KEYS=<kunci>:<NAMA_PROYEK>`) — JANGAN commit kunci ke git,
   JANGAN tampilkan ke user kecuali diminta.
3. Setiap kiriman wajib dwibahasa (`id` + `en`), maksimal 300 karakter judul /
   2000 karakter isi, bahasa pelayanan publik (dilarang menyebut library,
   framework, database, file kode, atau mekanisme internal).
4. Untuk rilis lama yang belum terkirim: ikuti `backfill.md` (sekali saja).
5. Laporkan ke user: apa yang dikirim (id baris), hasil verifikasi, dan apa
   pun yang gagal.
```

Aturan tambahan untuk AI (`AGENTS.md` ini):

- Kunci di `route.md` baris 2 adalah sumber kebenaran. Bila respons API `401`
  (tanpa header) atau `403` (kunci salah), berhenti dan minta kunci baru ke
  pemilik — jangan menebak.
- `source` HARUS stabil per rilis (nama berkasnya) agar revisi menimpa baris
  yang sama.
