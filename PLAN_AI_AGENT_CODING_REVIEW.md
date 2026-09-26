# Rencana AI Agent untuk Coding, Review, Audit, dan Push

## Tujuan

Membangun alur otomatis yang menerima tugas coding, membuat perubahan pada codebase, menjalankan pemeriksaan dan audit, memperbaiki kegagalan, lalu mengirim perubahan ke branch `dev` hanya jika seluruh gerbang kualitas lolos.

## Alur utama

1. **Terima tugas** — pengguna memberikan instruksi melalui CLI, issue, atau antarmuka sederhana. Agent merangkum ruang lingkup dan kriteria selesai.
2. **Siapkan workspace** — buat branch kerja terpisah dari `dev` agar perubahan yang belum lolos tidak mencemari branch tujuan.
3. **Coding** — agent membaca instruksi repo, memahami bagian codebase yang relevan, lalu mengimplementasikan tugas.
4. **Pemeriksaan otomatis** — jalankan format/lint, pemeriksaan tipe, tes yang relevan, dan build sesuai konfigurasi proyek.
5. **AI code review** — reviewer terpisah memeriksa bug, regresi, keamanan, penanganan error, dan kesesuaian perubahan dengan tugas. Reviewer harus memberi temuan yang bisa ditindaklanjuti beserta tingkat keparahan dan bukti.
6. **Audit** — periksa diff, file sensitif, dependensi, secret yang tidak sengaja masuk, serta kebijakan repository. Temuan kritis atau tinggi menggagalkan proses.
7. **Perbaikan berulang** — jika ada pemeriksaan atau review yang gagal, berikan hasilnya ke agent coding untuk diperbaiki, lalu ulangi pemeriksaan dan review. Batasi jumlah putaran (misalnya 3) agar agent tidak berputar tanpa akhir.
8. **Gerbang kelulusan** — lanjut hanya jika semua pemeriksaan wajib sukses, tidak ada temuan kritis/tinggi yang belum selesai, diff masih sesuai tugas, dan workspace bersih dari perubahan asing.
9. **Push ke `dev`** — setelah lolos, push perubahan ke branch `dev` sesuai kebijakan repo. Catat commit, ringkasan perubahan, hasil pemeriksaan, dan temuan yang tersisa.
10. **Gagal atau perlu keputusan** — jika batas perbaikan tercapai, pemeriksaan tidak tersedia, atau perubahan berisiko/di luar cakupan, hentikan alur dan laporkan alasan serta langkah berikutnya. Jangan push.

## Komponen yang dibutuhkan

- **Orchestrator** untuk mengatur tahapan, status, retry, timeout, dan pencatatan hasil.
- **Coding agent** untuk mengubah codebase.
- **Review agent** dengan instruksi dan konteks yang independen dari coding agent.
- **Runner terisolasi** untuk menjalankan perintah proyek tanpa memberi akses berlebih ke mesin atau kredensial.
- **Integrasi Git** untuk membuat branch kerja, membaca diff, membuat commit, dan push setelah gerbang lulus.
- **Konfigurasi proyek** yang menyatakan perintah tes/lint/build wajib dan kriteria audit.
- **Log/audit trail** yang menyimpan tugas, commit, hasil tiap pemeriksaan, review, dan keputusan push.

## Aturan keselamatan dan kualitas

- Agent bekerja pada branch terpisah; `dev` hanya diperbarui setelah seluruh gerbang lulus.
- Jangan memasukkan token, password, private key, atau kredensial ke prompt, log, maupun commit.
- Berikan akses tulis hanya ke workspace dan akses push hanya saat tahap push.
- Jangan menjalankan perintah destruktif atau mengubah konfigurasi CI/izin/branch protection tanpa aturan eksplisit.
- Kegagalan tes, audit, timeout, atau hasil reviewer yang tidak jelas berarti **gagal tertutup**: tidak ada push.
- Gunakan batas durasi, batas retry, dan batas biaya/token untuk tiap tugas.
- Simpan jejak keputusan agar hasil otomatis bisa ditinjau dan direproduksi.
- Terapkan proteksi branch `dev` di Git hosting; otomatisasi tidak menggantikan aturan proteksi repository.

## Kriteria lolos

Sebuah perubahan boleh dipush bila:

- semua pemeriksaan wajib selesai dengan status sukses;
- review tidak memiliki temuan kritis atau tinggi yang belum ditangani;
- audit secret dan kebijakan lolos;
- diff hanya memuat perubahan yang berkaitan dengan tugas;
- perubahan berhasil di-commit pada branch kerja yang benar;
- identitas dan kredensial push memiliki izin minimum yang diperlukan.

## Tahapan implementasi

### Tahap 1 — Otomatisasi lokal yang aman

Buat runner lokal yang menerima tugas, membuat branch kerja, menjalankan coding agent, dan mengumpulkan diff serta hasil perintah. Pada tahap awal, push dinonaktifkan agar alur bisa dievaluasi.

### Tahap 2 — Gerbang pemeriksaan dan review

Tambahkan konfigurasi perintah lint/test/build untuk repo ini, reviewer AI independen, audit diff/secret, batas retry, dan laporan hasil. Uji dengan tugas contoh serta perubahan yang sengaja dibuat gagal untuk memastikan sistem menolak perubahan.

### Tahap 3 — Push otomatis ke `dev`

Aktifkan push hanya setelah tahap sebelumnya konsisten. Gunakan kredensial khusus dengan izin minimum, lindungi branch, dan pastikan kegagalan atau hasil ambigu selalu menghentikan push.

### Tahap 4 — Operasional dan pemantauan

Tambahkan notifikasi hasil, metrik tingkat kelulusan dan kegagalan, pencatatan biaya/waktu, serta cara menghentikan atau membatalkan job yang sedang berjalan.

## Keputusan teknis yang perlu ditetapkan saat implementasi

- Cara memicu tugas: CLI, issue, atau antarmuka lain.
- Penyedia/model agent coding dan reviewer.
- Apakah push langsung ke `dev` diizinkan oleh kebijakan repo atau perlu pull request.
- Perintah verifikasi wajib dan tingkat keparahan temuan yang memblokir.
- Jumlah putaran perbaikan, timeout, batas biaya, serta lokasi log.
- Di mana runner berjalan dan bagaimana kredensial Git disediakan dengan aman.

## Kesimpulan

Alur ini memungkinkan coding sampai push berjalan otomatis. Keandalan utamanya bergantung pada pemeriksaan yang bisa dijalankan ulang, reviewer yang menghasilkan temuan terstruktur, batas perbaikan yang jelas, dan kebijakan gagal-tertutup. Untuk repository yang memakai proteksi branch atau pull request wajib, tahap akhir perlu mengikuti kebijakan tersebut alih-alih memaksa push langsung.
