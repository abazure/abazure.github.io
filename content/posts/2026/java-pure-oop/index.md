---
title: "Ternyata Java Bukanlah Bahasa OOP Murni! Ini Buktinya"
date: 2026-05-15
tags: ["java", "oop"]
description: "Java identik dengan OOP, tapi apakah ia benar-benar bahasa berorientasi objek murni? Jawabannya tidak. Ini alasannya dengan bahasa sederhana."
---

## Pendahuluan

Java sangat identik dengan **Object-Oriented Programming** (OOP). Tidak heran
banyak yang mengira Java adalah bahasa berorientasi objek **murni** (*pure
OOP*). Padahal kenyataannya: **Java bukan bahasa OOP murni.**

Apa maksudnya "murni"? Sebuah bahasa disebut OOP murni jika **segala sesuatunya
adalah objek** — bahkan angka sekalipun — dan semua operasi dilakukan lewat
_method_ pada objek. Java tidak sepenuhnya seperti itu.

Mari kita lihat alasan-alasannya.

## 1. Ada Tipe Primitif yang Bukan Objek

Java punya **tipe primitif** seperti `int`, `boolean`, `char`, dan `double`.
Nilai-nilai ini **bukan objek** — mereka disimpan langsung sebagai data mentah,
bukan sebagai instance dari sebuah *class*.

```java
int number = 10;        // primitif, bukan objek
Integer boxed = 20;     // objek (wrapper class)
```

## 2. Ada Anggota Statis (Static)

Anggota `static` (variabel atau metode) **menempel pada class, bukan pada objek**.
Artinya kita bisa memakainya **tanpa membuat objek** terlebih dahulu. Hal ini
sedikit menyimpang dari ide OOP murni, di mana segala sesuatu seharusnya melalui
objek.

```java
class MathUtil {
    static int square(int n) {
        return n * n;
    }
}

MathUtil.square(5);   // dipanggil tanpa 'new MathUtil()'
```

## 3. Ada Konstruksi Prosedural

Java punya perintah seperti perulangan (`for`, `while`) dan percabangan (`if`,
`switch`) yang bisa berdiri sendiri **di luar konteks objek**. Ini adalah ciri
gaya **prosedural**, bukan murni objek.

```java
for (int i = 0; i < 3; i++) {
    System.out.println(i);   // perulangan biasa, bukan lewat objek
}
```

## 4. Tidak Ada Pewarisan Banyak Class

Java **tidak mengizinkan** sebuah *class* mewarisi dari lebih dari satu *class*
sekaligus (untuk menghindari kebingungan seperti *diamond problem*). Sebagai
gantinya, Java hanya membolehkan banyak pewarisan lewat **interface**.

```java
// class Child extends Parent1, Parent2 { }   // <- TIDAK diizinkan

class Child extends Parent implements Skill1, Skill2 { }  // satu class + banyak interface
```

## 5. Operator Bawaan untuk Primitif

Operasi pada tipe primitif memakai **operator bawaan** seperti `+`, `-`, dan `*`,
bukan lewat pemanggilan metode pada objek. Di bahasa OOP murni, bahkan
penjumlahan pun sebenarnya adalah "pesan" yang dikirim ke sebuah objek.

```java
int sum = 3 + 4;     // pakai operator '+', bukan 3.add(4)
```

## Lalu, Bahasa Apa yang OOP Murni?

Beberapa bahasa yang dianggap **berorientasi objek murni** antara lain:

- **Smalltalk**
- **Ruby**
- **Eiffel**
- **Self**
- **Newspeak**

Di bahasa-bahasa ini, bahkan angka `3` pun adalah objek, dan `3 + 4` sebenarnya
berarti "kirim pesan `+` dengan argumen `4` ke objek `3`".

## Ringkasan

| Ciri Java | Kenapa Bukan OOP Murni |
|---|---|
| Tipe primitif (`int`, `boolean`) | Bukan objek |
| Anggota `static` | Menempel ke class, bukan objek |
| `for`, `if`, `while` | Konstruksi prosedural |
| Operator `+`, `-`, `*` | Bukan metode objek |
| Pewarisan tunggal | Multiple inheritance hanya lewat interface |

## Kesimpulan

Java adalah bahasa yang **sangat berorientasi objek**, tetapi **bukan OOP murni**.
Ia sengaja menggabungkan OOP dengan elemen prosedural dan tipe primitif demi
**kinerja** dan **kemudahan penggunaan**.

Jadi kalau ada yang bertanya "Apakah Java OOP murni?", jawabannya: **tidak**