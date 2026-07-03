---
title: "Cerita MongoDB-ku yang Mati Sendiri Setelah 30 Detik"
date: 2026-07-03
tags: ["mongodb", "podman", "linux", "cerita"]
description: "Pengalaman saya menelusuri kenapa container MongoDB 8 selalu mati setelah 30 detik, sampai akhirnya ketemu penyebab tak terduga di level CPU. Ternyata solusinya cuma satu baris."
---

## Awal Mula

Sore itu saya mau belajar database NoSQL yaitu MongoDB. Biasanya saya selalu prefer untuk menjalankan _service_ untuk _development local_ menggunakan _container_. Saya buat konfigurasi compose.yaml sederhana dan ketik `podman compose up -d`, cek dengan `podman ps`,
dan muncul tulisan **Up**. Lega. Saya pikir beres.

Tapi pas mau dipakai, koneksinya gagal. Saya cek lagi... container-nya sudah
**Exited**. Padahal tadi hidup. Saya coba nyalakan ulang hidup 30 detik, lalu
mati lagi. Saya ulangi sekali lagi, hasilnya sama persis. Selalu mati di detik
ke-30, seperti ada timer yang diam-diam menghitung mundur.

Di titik ini saya mulai penasaran. Bukan cuma kesal penasaran. Kok bisa
sesuatu mati begitu rapi dan teratur?

## Petunjuk Pertama: Angka di Belakang "Exited"

Saya perhatikan lebih teliti status container-nya:

```bash
podman ps -a
# mongo_mongodb_1   Exited (139)   docker.io/library/mongo:8
```

Ada angka **139** di situ. Ternyata angka ini bukan sembarang angka. Di Linux,
kalau sebuah program mati karena "ditembak" oleh sinyal, kodenya adalah
`128 + nomor sinyal`. Jadi `139 - 128 = 11`, dan sinyal nomor 11 itu namanya
**SIGSEGV** alias program-nya *crash* karena mengakses memori yang tidak boleh.

> Program selesai normal → exit code 0-127<br>
> Program mati karena sinyal → exit code 128 + nomor sinyal

Ini bikin saya sedikit tenang, karena artinya bukan salah ketik password atau
salah tulis compose. MongoDB-nya benar-benar *crash*. Pertanyaannya sekarang:
kenapa?

## Log-nya Malah Terlihat Baik-Baik Saja

Biasanya kalau ada masalah, saya langsung baca log. Tapi kali ini justru
membingungkan, karena log-nya kelihatan **normal**:

```text
"msg":"mongod startup complete"
"msg":"Waiting for connections","attr":{"port":27017}
```

MongoDB berhasil menyala, siap menerima koneksi, dan... tidak ada satu pun pesan
error.

Dari sini saya belajar satu hal: kalau sebuah service mati tetapi log-nya bersih, bisa jadi prosesnya crash sebelum aplikasi sempat menuliskan pesan error. Artinya, saya harus mencari petunjuk di tempat lain.

## Bertanya ke Sistem Operasi

Kalau MongoDB tidak sempat menulis surat wasiatnya, mungkin sistem operasi
mencatatnya. Di Linux ada perintah `coredumpctl` yang menyimpan daftar program
yang *crash*. Saya coba:

```bash
coredumpctl list
# ... SIGSEGV  /usr/bin/mongod
# ... SIGSEGV  /usr/bin/mongod
# ... SIGSEGV  /usr/bin/mongod
```

Deretan `SIGSEGV` tersebut menunjukkan bahwa mongod berulang kali mengalami
crash satu entri untuk setiap kali saya mencoba menjalankan ulang
container.

Setelah ditelusuri lebih jauh, ternyata yang crash bukan proses utama
MongoDB, melainkan sebuah proses latar belakang yang mulai berjalan sekitar
30 detik setelah startup. Inilah yang menjelaskan kenapa container selalu
berhenti secara konsisten beberapa saat setelah berhasil menyala.

## Menebak? Tidak. Saya Coba Buktikan

Saya sempat curiga: jangan-jangan ini khusus MongoDB versi 8? Daripada menebak,
saya putuskan untuk mengujinya langsung. Saya jalankan **MongoDB versi 7** dengan
pengaturan yang sama persis, lalu saya tunggu.

```bash
podman run -d --name mongo_test \
  -e MONGO_INITDB_ROOT_USERNAME=roni \
  -e MONGO_INITDB_ROOT_PASSWORD=abazure \
  docker.io/mongo:7
```

Saya tunggu 30 detik... masih hidup. 50 detik... masih hidup. **MongoDB 7 aman.**
Sementara MongoDB 8 selalu mati. Berarti masalahnya memang khusus di MongoDB 8 di
komputer saya ini bukan di compose, bukan di data.

Momen ini penting buat saya: dengan mengubah **satu hal saja** (versinya) dan
melihat bedanya, saya jadi tahu persis di mana masalahnya. Jauh lebih pasti
daripada sekadar menduga-duga.

## Ternyata Masalahnya di CPU

Nah, ini bagian yang paling tak terduga. Kenapa program yang sama bisa jalan di
komputer lain tapi *crash* di komputer saya? Jawabannya ada di **prosesor** dan
kernel Linux yang saya pakai (saya pakai CachyOS yang memang versinya baru banget).

> **Sebentar, apa itu CachyOS?** Buat yang belum tahu, CachyOS adalah sistem
> operasi Linux (turunan Arch Linux) yang fokus pada **kecepatan**. Yang bikin
> beda, CachyOS memakai kernel dan program-program versi paling baru, sering kali
> dengan fitur-fitur eksperimental yang belum ada di Linux "biasa" seperti Ubuntu.
> Enaknya: performanya kencang dan selalu terdepan. Konsekuensinya: kadang muncul
> masalah aneh seperti yang saya alami ini karena fitur barunya (dalam kasus ini
> Shadow Stack) belum tentu cocok dengan semua program lama. Jadi wajar kalau
> pengguna distro yang lebih "stabil" mungkin tidak pernah mengalami crash ini.

Setelah ditelusuri lebih jauh, ternyata penyebabnya bukan berasal dari MongoDB itu sendiri, melainkan dari fitur keamanan prosesor Intel yang diaktifkan oleh kernel Linux, yaitu **Shadow Stack**.

Secara sederhana, **Shadow Stack** bekerja seperti lapisan pemeriksaan tambahan untuk memastikan sebuah program benar-benar kembali ke jalur eksekusi yang semestinya setelah sebuah fungsi selesai dijalankan. Jika ada ketidaksesuaian, prosesor akan menganggapnya sebagai perilaku yang mencurigakan dan langsung menghentikan program demi keamanan.

Pada kasus ini, MongoDB 8 ternyata menggunakan mekanisme tertentu yang tidak sepenuhnya sesuai dengan ekspektasi **Shadow Stack**. Akibatnya, prosesor menganggap perilaku tersebut mencurigakan dan menghentikan proses mongod, yang kemudian terlihat sebagai crash. MongoDB 7 tidak memicu pemeriksaan ini, sehingga dapat berjalan tanpa masalah pada sistem yang sama.
## Solusinya Cuma Satu Baris

Yang bikin lega, fitur **Shadow Stack** ini bisa dimatikan **khusus untuk MongoDB
saja**, tanpa perlu mengutak-atik kernel atau mengorbankan keamanan program lain.
Saya cukup menambahkan satu baris di compose:

```yaml
services:
  mongodb:
    image: docker.io/mongo:8
    environment:
      MONGO_INITDB_ROOT_USERNAME: roni
      MONGO_INITDB_ROOT_PASSWORD: abazure
      GLIBC_TUNABLES: glibc.cpu.hwcaps=-SHSTK   # baris penyelamat
    volumes:
      - mongodb_data:/data/db

volumes:
  mongodb_data:
```

Baris `GLIBC_TUNABLES: glibc.cpu.hwcaps=-SHSTK` ini pada dasarnya bilang ke
MongoDB: "sudah, abaikan saja pengecekan Shadow Stack itu". Ibaratnya saya kasih
kartu akses khusus ke satu tamu tepercaya, sementara tamu lain tetap diperiksa
seperti biasa.

Setelah saya jalankan ulang, container-nya bertahan lebih dari satu menit lewat
jauh dari batas 30 detik yang biasanya. Saya tes koneksinya:

```bash
podman exec mongo_mongodb_1 mongosh -u roni -p abazure \
  --quiet --eval 'db.runCommand({ping:1}).ok'
# 1
```

Angka `1` muncul. Artinya MongoDB benar-benar hidup dan bisa dipakai. Akhirnya.

## Penutup

Yang paling saya ingat dari pengalaman ini: **gejalanya sering menipu.** Saya
sempat curiga compose saya salah, curiga password saya keliru, curiga datanya
rusak padahal semuanya baik-baik saja. Masalah sebenarnya ada di tempat yang
bahkan tidak saya sangka, jauh di bawah, di antara MongoDB dan prosesor.

Kuncinya ternyata bukan menebak, tapi mengikuti jejak satu per satu: dari angka
139, ke catatan *crash* di sistem, lalu membuktikan lewat percobaan. Pelan tapi
pasti. Dan pada akhirnya, masalah yang tadinya bikin frustrasi ternyata bisa
selesai cukup dengan satu baris asal saya mau memahaminya sampai ke akar.

Buat saya, itu justru bagian paling menyenangkan dari ngoprek: bukan sekadar
memperbaiki, tapi jadi benar-benar mengerti apa yang terjadi.
