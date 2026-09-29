# Pipeline Multi-Agent untuk Claude Code (port dari Antigravity-Pipline)

Ini adalah versi Claude Code dari [anggarasa/Antigravity-Pipline](https://github.com/anggarasa/Antigravity-Pipline) — tim development AI otonom (PM, Designer, Frontend, Backend, Mobile, QA, DevOps, Curator) yang tech-agnostic dan belajar dari tiap siklus kerja.

Bukan hasil copy-paste langsung: repo aslinya dibuat untuk Antigravity IDE, jadi strukturnya diterjemahkan ulang memakai fitur native Claude Code (subagents, skills/slash command, dan **persistent agent memory**) supaya perilakunya sama tapi mekanismenya sesuai cara Claude Code bekerja.

## Instalasi

1. Copy folder `.claude/` dan file `CLAUDE.md` ini ke root proyekmu (baik proyek baru maupun yang sudah berjalan).
2. Jalankan `claude` di root proyek itu.
3. Pakai salah satu command:
   ```
   /startcycle "Saya butuh aplikasi kasir untuk toko retail, ada web admin dan aplikasi mobile untuk kasir"
   /addfeature "Tambahkan fitur export laporan penjualan ke Excel"
   /fixbug "Tombol checkout tidak merespon saat keranjang lebih dari 10 item"
   ```

Commit `.claude/agents/` dan `.claude/skills/` ke git supaya seluruh tim kerja dengan pipeline yang sama. `.claude/agent-memory/` (dibuat otomatis saat pipeline jalan) sebaiknya **ikut di-commit juga** — itu memori bersama tim, bukan data pribadi.

## Struktur

```
CLAUDE.md                          # aturan inti, SENGAJA dibuat sangat singkat
.claude/
├── agents/                        # 9 sub-agent, setara "8 persona" di repo asli + 1 tambahan (scout)
│   ├── scout.md                   # onboarding proyek (pengganti onboarding/read_project.md)
│   ├── pm.md
│   ├── designer.md
│   ├── frontend.md
│   ├── backend.md
│   ├── mobile.md
│   ├── qa.md
│   ├── devops.md
│   └── curator.md                 # inti self-learning
├── skills/                        # setara workflows/*.md (slash command)
│   ├── startcycle/SKILL.md        # /startcycle
│   ├── addfeature/SKILL.md        # /addfeature
│   └── fixbug/SKILL.md            # /fixbug
└── agent-memory/                  # dibuat otomatis oleh Claude Code — pengganti "Section B" self-learning
    └── <nama-agent>/MEMORY.md
production_artifacts/              # output tiap siklus: spesifikasi, design system, temuan QA
```

## Pemetaan konsep: repo asli → Claude Code

| Repo asli (Antigravity)                          | Versi Claude Code                                                              | Kenapa begini                                                                                                   |
| ------------------------------------------------- | ------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `agents.md` (8 persona dalam 1 file, selalu dibaca) | `CLAUDE.md` sangat singkat + 9 file terpisah di `.claude/agents/`               | Tiap subagent baru memuat file-nya sendiri **saat dipanggil**, bukan semuanya sekaligus di awal tiap giliran chat. |
| `workflows/*.md` (slash command)                  | `.claude/skills/*/SKILL.md` dengan `disable-model-invocation: true`             | Format command Claude Code untuk hal yang harus dipicu manual oleh user, bukan otomatis oleh model.                |
| `knowledge/*.md` Section A (aturan permanen)       | Ditulis langsung di badan tiap file agent                                       | Hanya termuat saat agent itu dipanggil — bukan aturan global yang selalu ada di context.                          |
| `knowledge/*.md` Section B (pelajaran dinamis, dikelola manual oleh curator) | Fitur native **`memory: project`** pada subagent → `.claude/agent-memory/<nama>/MEMORY.md` | Claude Code sudah punya mekanisme "memori persisten per-subagent lintas sesi" bawaan — tidak perlu direkayasa manual lewat file knowledge base raksasa yang dibaca ulang tiap siklus. |
| `onboarding/read_project.md` + fast-path manual    | Subagent `scout` dengan `memory: project` + model `haiku`                       | Fast-path "skip full scan kalau context masih segar" jadi otomatis lewat cek memori, dan modelnya murah karena tugasnya sebagian besar cuma listing file. |
| `curator/update_knowledge.md`                      | Subagent `curator`                                                              | Tetap dipertahankan sebagai langkah eksplisit terakhir tiap siklus — dia yang tulis ke memori agent lain, dengan cek anti-duplikasi seperti aslinya. |

## Kenapa desain ini hemat token (dibanding satu sesi besar tanpa pipeline)

1. **CLAUDE.md minimal** — ini satu-satunya file yang termuat di SETIAP giliran percakapan, jadi sengaja dibuat sangat pendek. Semua detail peran ada di file agent yang hanya termuat saat agent itu dipanggil (lihat dokumentasi resmi: [Reduce token usage](https://code.claude.com/docs/en/costs#reduce-token-usage) — poin "Move instructions from CLAUDE.md to skills").
2. **Subagent = context terisolasi** — eksplorasi/pekerjaan verbose tiap peran (baca banyak file, coba-coba command) tidak "mengotori" context percakapan utama; yang kembali cuma ringkasannya.
3. **`scout` pakai model murah (haiku)** dan fast-path memori — proyek yang sama tidak di-scan ulang total di setiap `/addfeature`/`/fixbug`.
4. **`qa` audit terarah** — untuk `/addfeature` dan `/fixbug`, hanya file yang berubah yang diaudit, bukan seluruh codebase tiap kali.
5. **Memori persisten per-agent** menggantikan pola "baca ulang knowledge base raksasa tiap siklus" — tiap agent cuma baca memorinya sendiri (yang dijaga ringkas oleh curator), bukan satu file knowledge base yang terus membengkak dan ikut dibaca semua agent lain.

## Batasan yang perlu diketahui

- Ini **bukan** cara untuk membuat limit/kuota Claude Code jadi tidak terbatas — ini cara mengurangi token yang terbuang untuk hal yang tidak perlu (re-scan berulang, context yang tidak relevan ikut kebawa), supaya limit yang ada dipakai lebih efisien untuk kerja yang benar-benar produktif.
- Delegasi ke subagent tetap memakan token (task/summary tetap masuk context percakapan utama) — pipeline ini mengoptimalkan, bukan menghilangkan, biaya token.
- Sesuaikan daftar `tools:` di tiap file agent kalau proyekmu butuh tool tambahan (misalnya akses MCP server tertentu untuk `devops`).
