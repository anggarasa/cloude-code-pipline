---
name: addfeature
description: Tambah fitur baru ke proyek yang sudah berjalan. Hanya dijalankan lewat perintah /addfeature secara eksplisit.
disable-model-invocation: true
argument-hint: [deskripsi fitur]
---

Deskripsi fitur dari user: $ARGUMENTS

Orkestrasikan alur berikut, sama seperti `/startcycle` tapi lebih ringan karena ini proyek existing — manfaatkan fast-path tiap sub-agent (mereka sudah tahu cara ini lewat memori masing-masing).

### Urutan eksekusi

0. Delegasikan ke sub-agent **scout** untuk konfirmasi konteks proyek (dia akan pakai fast-path dari memorinya kalau memang belum ada perubahan besar — jangan minta dia full scan tanpa alasan).

1. Delegasikan ke sub-agent **pm** untuk menyusun/update `production_artifacts/Technical_Specification.md` khusus fitur ini, dengan konteks proyek existing dari scout. Tunggu persetujuan user sebelum lanjut, sama seperti di `/startcycle`.

2. Kalau fitur ini butuh perubahan visual signifikan, delegasikan ke **designer** untuk update `Design_System.md` yang relevan. Kalau tidak (misal fitur backend-only), skip langkah ini.

3. Delegasikan ke engineer yang relevan (**backend**/**frontend**/**mobile**) untuk implementasi fitur sesuai spesifikasi.

4. Delegasikan ke sub-agent **qa**, dengan instruksi eksplisit: **hanya audit file yang berubah di siklus ini**, bukan seluruh codebase.

5. Delegasikan ke sub-agent **devops** kalau fitur ini butuh perubahan cara install/jalankan/deploy. Kalau tidak ada perubahan di area itu, boleh skip dan cukup pastikan aplikasi masih jalan normal.

6. Delegasikan ke sub-agent **curator** dengan ringkasan siklus ini untuk update memori agent yang relevan.

7. Tutup dengan ringkasan akhir: apa yang ditambahkan, di mana lokasinya, dan pelajaran baru apa yang disimpan curator.
