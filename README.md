# activity-sync

Paket mandiri agar AI di proyek mana pun otomatis mengirim kabar kegiatan ke
portfolio (`https://ajoroby.is-a.dev`). Salin folder ini ke proyek lain, ikuti
`AGENTS.md`, selesai.

| `AGENTS.md` | Blok aturan siap tempel ke `AGENTS.md` proyek lain |
| `skill-activity-sync.md` | Panduan pemilik portfolio (kunci, registry, alur data) — arsip, jangan disebar ke app lain |
| `README.md` | Berkas ini |

## Cara pakai (ringkas)

1. Salin folder ini → `.agents/skills/activity-sync/` di proyek tujuan.
2. Tempel blok dari `AGENTS.md` ke `AGENTS.md` proyek tujuan.
3. Siapkan kunci: `cp .ACTIVITY_PROJECT_KEYS.example .ACTIVITY_PROJECT_KEYS`
   lalu isi kunci `X-project` app tersebut — AI membaca berkas itu sebagai
   sumber kuncinya; berkas asli jangan di-commit.
4. Uji: kirim contoh dari `SKILL.md`, verifikasi via `GET /api/activities?limit=3`.
   Atau import `postman-collection.json` ke Postman untuk klik-klik manual.
5. Setelah itu AI wajib mengirim setiap ada perubahan yang dirasakan pengguna,
   dengan `source` = nama berkas rilis agar revisi menimpa, bukan mendobel.
