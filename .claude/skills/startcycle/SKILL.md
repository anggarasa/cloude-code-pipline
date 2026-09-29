---
name: startcycle
description: Mulai siklus pengembangan penuh dari ide baru (web, backend, mobile, atau kombinasinya). Hanya dijalankan lewat perintah /startcycle secara eksplisit.
disable-model-invocation: true
argument-hint: [deskripsi ide]
---

Ide dari user: $ARGUMENTS

Orkestrasikan proses berikut secara berurutan dengan mendelegasikan ke sub-agent yang sesuai (lihat `.claude/agents/`). WAJIB pakai Bahasa Indonesia sederhana di setiap ringkasan ke user, dan JANGAN kerjakan sendiri bagian yang seharusnya didelegasikan.

### Urutan eksekusi

0. Delegasikan ke sub-agent **scout** untuk memahami struktur & konteks proyek (baru atau existing). Jangan lanjut sebelum ini selesai.

1. Delegasikan ke sub-agent **pm** untuk menyusun `production_artifacts/Technical_Specification.md` dari ide di atas, dengan konteks dari scout.
   Tampilkan spesifikasinya ke user dan **WAJIB berhenti, tunggu persetujuan eksplisit** ("Setuju"/"Approved") sebelum lanjut. Kalau user kasih revisi, delegasikan lagi ke pm dengan catatan revisinya, ulangi sampai disetujui.

2. Delegasikan ke sub-agent **designer** untuk menyusun `production_artifacts/Design_System.md` berdasarkan spesifikasi yang disetujui. Tunggu persetujuan singkat, kecuali user sudah bilang "lanjut tanpa review desain".

3. Berdasarkan bagian "Tech Stack" di spesifikasi, delegasikan ke engineer yang relevan (bisa lebih dari satu; kalau frontend butuh kontrak API dari backend, jalankan backend dulu):
   - Ada backend/API → sub-agent **backend**.
   - Ada frontend web → sub-agent **frontend**.
   - Ada aplikasi mobile → sub-agent **mobile**.

4. Delegasikan ke sub-agent **qa** untuk audit seluruh kode baru di siklus ini. Minta dia mencatat temuan ke `production_artifacts/QA_Findings.md` dan memperbaiki langsung apa yang bisa dia tangani.

5. Delegasikan ke sub-agent **devops** untuk deteksi stack, install dependency, dan jalankan/deploy aplikasi.

6. Delegasikan ke sub-agent **curator** dengan ringkasan hasil kerja tiap agent + isi `QA_Findings.md`, supaya dia menyimpan pelajaran baru ke memori agent yang relevan.

7. Tutup dengan ringkasan akhir ke user: apa yang berhasil dibangun, lokasinya, cara menjalankannya/link lokal, dan pelajaran baru apa yang barusan disimpan curator.
