---
name: fixbug
description: Perbaiki bug yang dilaporkan user di proyek yang sudah berjalan. Hanya dijalankan lewat perintah /fixbug secara eksplisit.
disable-model-invocation: true
argument-hint: [deskripsi bug]
---

Deskripsi bug dari user: $ARGUMENTS

Ini alur paling ringan di antara tiga command — TIDAK perlu pm/designer kecuali bug ternyata mengungkap masalah desain/spesifikasi yang lebih besar.

### Urutan eksekusi

0. Delegasikan ke sub-agent **scout** untuk konfirmasi konteks proyek lewat fast-path (jangan full scan kecuali memang perlu).

1. Delegasikan ke engineer yang paling relevan dengan lokasi bug (**backend**/**frontend**/**mobile**) untuk:
   - Reproduksi bug berdasarkan deskripsi.
   - Cari root cause (bukan cuma tempel patch di gejala).
   - Perbaiki, dan cek memorinya dulu — barangkali kategori bug ini sudah pernah tercatat dengan cara pencegahannya.

   Kalau bug butuh koordinasi lintas layer (misal bug muncul karena kontrak API tidak sesuai antara frontend & backend), delegasikan ke kedua sub-agent tersebut secara berurutan.

2. Delegasikan ke sub-agent **qa** untuk verifikasi perbaikan — audit HANYA file yang barusan diubah, cek juga tidak ada regresi di area terdekat.

3. Kalau perbaikan ini butuh perubahan cara jalanin/deploy aplikasi, delegasikan ke **devops** singkat untuk verifikasi. Kalau tidak, skip.

4. Delegasikan ke sub-agent **curator** dengan ringkasan root cause & perbaikan, supaya kategori bug ini masuk memori agent yang relevan untuk mencegah terulang.

5. Tutup dengan ringkasan akhir: apa penyebab bugnya, apa perbaikannya, dan sudah diverifikasi bagaimana.
