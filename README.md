# activity-sync

Paket mandiri agar AI di proyek mana pun otomatis mengirim kabar kegiatan ke
portfolio (`https://ajoroby.is-a.dev`). Salin folder ini ke proyek lain, ikuti
`AGENTS.md`, selesai.

## Isi paket

| Berkas | Untuk |
|---|---|
| `SKILL.md` | Aturan + alur CRUD untuk AI (`<proyek>/.agents/skills/activity-sync/SKILL.md`) |
| `route.md` | Kontrak API lengkap (POST/GET/PATCH/DELETE, kode status, batas) |
| `.ACTIVITY_PROJECT_KEYS.example` | Pola berkas kunci `X-project` — salin jadi `.ACTIVITY_PROJECT_KEYS` lalu isi kunci app |
| `backfill.md` | Kirim ulang `releases/*.json` lama (sekali saja per proyek, OS-agnostik) |
| `postman-collection.json` | Uji coba manual semua route (import ke Postman, isi variable `projectKey`) |
| `AGENTS.md` | Blok aturan siap tempel ke `AGENTS.md` proyek lain |
| `README.md` | Berkas ini |

## Cara pakai (ringkas)

1. Salin folder ini → `.agents/skills/activity-sync/` di proyek tujuan.
2. Tempel blok dari `AGENTS.md` ke `AGENTS.md` proyek tujuan.
3. Siapkan kunci: `cp .ACTIVITY_PROJECT_KEYS.example .ACTIVITY_PROJECT_KEYS`
   lalu isi kunci `X-project` app tersebut (default contoh = kunci bersama).
4. Uji: kirim contoh dari `SKILL.md`, verifikasi via `GET /api/activities?limit=3`.
   Atau import `postman-collection.json` ke Postman untuk klik-klik manual.

## Keamanan kunci

Kunci ini **sengaja publik dalam paket** agar AI bisa bekerja tanpa setup.
Risikonya terbatas: hanya bisa menitip kabar (dimoderasi admin portfolio).
Bila disalahgunakan: pemilik mencabut kunci dari registry + redeploy, kiriman
dengan kunci itu langsung ditolak (`403`). Jangan pernah memakai kunci ini
untuk hal selain kirim kegiatan.
