---
title: "Mengenal Apa Itu Proses di Linux"
date: 2026-05-10
tags: ["linux", "sysadmin"]
cover: "linux-process.webp"
description: "Pernah dengar istilah PID ? Memahami cara kerja proses di dalam sistem Linux!"
---

## Pendahuluan

Di Linux, setiap aplikasi atau perintah yang sedang berjalan disebut sebagai **Proses**. 

Sederhananya, jika sebuah program adalah resep masakan yang tertulis di buku, maka proses adalah kegiatan memasaknya di dapur. Tanpa proses, kode program hanyalah tumpukan file mati di dalam harddisk. Memahami cara Linux mengelola proses sangat penting, terutama buat kamu yang ingin belajar administrasi sistem atau sekadar ingin tahu kenapa komputer bisa melakukan banyak hal sekaligus.

## Apa Itu Proses?

Secara teknis, **proses adalah instansi dari sebuah program yang sedang dieksekusi**. Di Linux, proses bersifat dinamis; mereka bisa berada dalam berbagai kondisi (*state*) seperti sedang berjalan (*running*), menunggu antrean (*waiting*), atau sedang tidur (*sleeping*) tergantung pada kesibukan sistem.

### Identitas Sebuah Proses
Setiap proses yang lahir di Linux diberikan identitas unik agar sistem tidak bingung. Berikut adalah identitas utamanya:

*   **PID (Process ID):** Angka unik untuk setiap proses.
*   **PPID (Parent Process ID):** ID dari proses "orang tua" yang memicu lahirnya proses tersebut.
*   **PGID (Process Group ID):** ID kelompok proses tersebut bernaung.

Selain angka-angka ini, sebuah proses juga membawa "bekal" berupa kode program, data, variabel, hingga hak akses tertentu.

---

## Bagaimana Linux Mengatur Proses?

Bayangkan CPU adalah seorang koki dan proses adalah pesanan pelanggan. Karena kokinya terbatas tapi pesanannya banyak, Linux harus pintar mengatur jadwal.

### 1. Penjadwalan (Scheduling)
Linux menggunakan sistem **preemptive scheduling**. Artinya, hanya Kernel (inti sistem) yang punya hak penuh untuk memutus sebuah proses yang sedang asyik menggunakan CPU demi memberikan giliran kepada proses lain. Proses tidak bisa saling serobot sendiri!

### 2. Context Switching
Agar kita bisa buka browser sambil dengerin musik, Linux melakukan trik yang disebut *Context Switching*. Sistem akan mengambil "foto" (snapshot) kondisi terakhir sebuah proses, menyimpannya, lalu menjalankan proses lain. Ini terjadi sangat cepat sehingga kita merasa semuanya berjalan bersamaan.

### 3. Isolasi Proses
Setiap proses berjalan di ruangnya sendiri (*user space*). Jadi, kalau ada satu aplikasi yang *crash* atau error, dia tidak akan langsung menjatuhkan aplikasi lainnya. Ini yang membuat Linux dikenal sangat stabil.

---

## Hak Akses dan Keamanan: setuid

Biasanya, proses akan berjalan dengan izin akses sesuai user yang menjalankannya. Namun, ada fitur unik bernama **setuid**. 

Misalnya saat kamu mengganti password dengan perintah `passwd`. Sebenarnya, file yang menyimpan password bersifat rahasia dan hanya bisa diubah oleh `root`. Dengan *setuid bit*, perintah tersebut bisa berjalan sementara dengan level "Superuser" agar bisa mengupdate file sistem, meskipun dijalankan oleh user biasa.

> **Catatan:** Program dengan *setuid* harus dikelola dengan hati-hati karena bisa menjadi celah keamanan jika dieksploitasi.

---

## Ringkasan Atribut & Perintah

Berikut adalah tabel cepat untuk memahami atribut proses dan perintah yang bisa kamu gunakan:

| Komponen | Penjelasan Singkat |
|---|---|
| **Program** | Kode mentah yang sedang dijalankan |
| **Context** | Snapshot memori dan register CPU saat ini |
| **Permissions** | Hak akses (User ID & Group ID) |
| **Syscall** | Jembatan proses untuk minta izin akses hardware ke Kernel |

### Perintah Berguna untuk Eksplorasi
Mau coba lihat proses di komputermu sendiri? Coba ketik perintah ini di terminal:

*   `ps aux` : Menampilkan seluruh proses yang sedang berjalan secara detail.
*   `top` atau `htop` : Memonitor proses secara *real-time* (mirip Task Manager).
*   `pstree` : Melihat silsilah "keluarga" proses dalam format pohon.

## Kesimpulan

Proses adalah fondasi utama bagaimana multitasking bekerja di Linux. Dengan adanya PID untuk identitas, *scheduler* untuk pembagian tugas, dan isolasi proses untuk keamanan, Linux bisa menjalankan ribuan tugas secara efisien tanpa saling tabrakan.
