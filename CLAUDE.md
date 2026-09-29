# Tim Development Otonom (Multi-Agent Pipeline)

Proyek ini pakai tim sub-agent Claude Code untuk kerja terstruktur, tech-agnostic, dan hemat token dibanding satu sesi besar yang mengerjakan semuanya sendiri.

## Cara pakai

- `/startcycle <ide>` — mulai proyek baru / fitur besar dari nol.
- `/addfeature <deskripsi>` — tambah fitur ke proyek yang sudah berjalan.
- `/fixbug <deskripsi>` — perbaiki bug yang dilaporkan.

Ketiga command di atas mengorkestrasi sub-agent di `.claude/agents/` secara berurutan: `scout` → `pm` → `designer` → `frontend`/`backend`/`mobile` → `qa` → `devops` → `curator`.

## Aturan wajib untuk SEMUA agent (termasuk kamu sendiri di percakapan utama)

- Komunikasi ke user: Bahasa Indonesia yang sederhana dan jelas.
- Clean code: komentar hanya di bagian penting/kompleks/sensitif (logic keamanan, kalkulasi bisnis, workaround) — bukan di setiap baris.
- Tidak pernah hardcode secret/API key/credential — selalu lewat environment variable.
- Delegasikan ke sub-agent yang tepat alih-alih mengerjakan semuanya di percakapan utama — itu yang menjaga context window kamu tetap kecil dan biaya token tetap rendah.

## Catatan penting soal desain pipeline ini

Detail peran, batasan, dan checklist tiap agent **sengaja tidak ditaruh di sini** — masing-masing ada di file agentnya sendiri (`.claude/agents/*.md`) dan hanya dimuat ke context saat agent itu benar-benar dipanggil. Kalau semua detail ditaruh di CLAUDE.md ini, itu akan ikut termuat di SETIAP giliran percakapan, termasuk saat tidak relevan — boros token. Lihat `README.md` di root repo untuk penjelasan lengkap desainnya.
