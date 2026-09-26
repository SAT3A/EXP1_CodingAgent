# Rencana MVP: TaskLite

## Tujuan

Buat aplikasi web sederhana untuk mencatat tugas harian. Aplikasi ini menjadi tugas uji bagi workflow coding agent: spesifikasi cukup kecil, hasilnya mudah dilihat, dan kriteria selesai bisa diperiksa.

## Pengguna

Seseorang yang ingin mencatat tugas dengan cepat tanpa membuat akun.

## Teknologi

- HTML, CSS, dan JavaScript tanpa framework.
- Simpan data di `localStorage`; tidak perlu server atau database.
- Jalankan secara lokal dengan membuka `index.html`.

## Fitur MVP

1. Tambah tugas dengan judul wajib.
2. Tandai tugas selesai atau belum selesai.
3. Hapus tugas.
4. Filter daftar: semua, aktif, atau selesai.
5. Simpan perubahan otomatis sehingga tugas tetap ada setelah halaman dimuat ulang.
6. Tampilkan pesan yang jelas saat daftar tugas kosong.

Setiap tugas memiliki ID unik, judul, status selesai, dan waktu dibuat.

## Tampilan

- Satu halaman dengan judul “TaskLite”, kolom input, tombol tambah, filter, dan daftar tugas.
- Layout nyaman dipakai di layar desktop maupun ponsel.
- Tombol dan input memiliki label yang jelas; semua fungsi utama bisa digunakan dengan keyboard.
- Tugas selesai terlihat berbeda dari tugas aktif.

## Kriteria selesai

- Judul kosong atau hanya spasi tidak bisa ditambahkan; tampilkan pesan validasi yang mudah dimengerti.
- Menambahkan judul yang valid menampilkan tugas baru dan membersihkan kolom input.
- Tombol selesai mengubah status tugas tanpa menghapus judulnya.
- Tombol hapus menghilangkan tugas yang dipilih.
- Ketiga filter hanya menampilkan tugas yang sesuai.
- Menambah, menyelesaikan, dan menghapus tugas tersimpan setelah halaman dimuat ulang.
- Data `localStorage` yang rusak atau tidak valid tidak membuat aplikasi gagal dibuka.
- Tidak ada error JavaScript saat alur utama digunakan.

## Di luar cakupan MVP

- Akun pengguna, sinkronisasi cloud, kolaborasi, tanggal jatuh tempo, kategori, dan notifikasi.
- Backend, database, atau dependensi framework.

## Tugas untuk coding agent

Implementasikan MVP TaskLite dari spesifikasi ini. Sebelum selesai, periksa alur tambah, validasi, selesai/belum selesai, hapus, filter, dan persistensi. Laporkan file yang diubah, pemeriksaan yang dijalankan, hasilnya, dan risiko yang belum terselesaikan. Jangan menambahkan fitur di luar cakupan.
