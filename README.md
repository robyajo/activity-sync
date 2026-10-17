# activity-sync

Paket mandiri agar AI di proyek mana pun otomatis menulis catatan rilis dan
mengirim kabar kegiatannya ke portfolio (`https://ajoroby.is-a.dev`).
Salin folder ini ke proyek lain, ikuti `AGENTS.md`, selesai.

## Isi paket

| Berkas | Untuk |
|---|---|
| `SKILL.md` | Aturan + alur CRUD untuk AI (`<proyek>/.agents/skills/activity-sync/SKILL.md`) |
| `route.md` | Kontrak API lengkap (POST/GET/PATCH/DELETE, kode status, batas) |
| `release-notes/SKILL.md` | Skill menulis berkas rilis harian `releases/YYYY-MM-DD-*.json` di app tujuan — bentuk yang persis diterima API |
| `.ACTIVITY_PROJECT_KEYS.example` | Pola berkas kunci `X-project` — salin jadi `.ACTIVITY_PROJECT_KEYS` lalu isi kunci app |
| `backfill.md` | Kirim ulang `releases/*.json` lama (sekali saja per proyek, OS-agnostik) |
| `postman-collection.json` | Uji coba manual semua route (import ke Postman, isi variable `projectKey`) |
| `AGENTS.md` | Blok aturan siap tempel ke `AGENTS.md` proyek lain |
| `skill-activity-sync.md` | Panduan pemilik portfolio (kunci, registry, alur data) — arsip, jangan disebar ke app lain |
| `README.md` | Berkas ini |

## Cara pakai (ringkas)

1. Salin folder ini → `.agents/skills/activity-sync/` di proyek tujuan.
2. Tempel blok dari `AGENTS.md` ke `AGENTS.md` proyek tujuan.
3. Siapkan kunci: `cp .ACTIVITY_PROJECT_KEYS.example .ACTIVITY_PROJECT_KEYS`
   lalu isi kunci `X-project` app tersebut — AI membaca berkas itu sebagai
   sumber kuncinya; berkas asli jangan di-commit.
4. Setiap selesai kerja: tulis berkas rilis memakai skill `release-notes`
   (`release-notes/SKILL.md`, 1 berkas JSON per hari, dwibahasa), lalu kirim
   isi berkas itu ke portfolio lewat `SKILL.md` (+ `project.name`).
5. Uji: kirim contoh dari `SKILL.md`, verifikasi via `GET /api/activities?limit=3`.
   Atau import `postman-collection.json` ke Postman untuk klik-klik manual.

## Keamanan kunci

Kunci bersama hanya boleh dipakai untuk mengirim kegiatan ke portfolio
(dimoderasi pemilik). Bila disalahgunakan, pemilik mencabutnya dari registry
+ redeploy — kiriman dengan kunci itu langsung ditolak (`403`).
