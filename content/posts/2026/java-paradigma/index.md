---
title: "Paradigma Pemrograman yang Didukung Java"
date: 2026-06-23
tags: ["java", "programming"]
description: "Java bukan cuma bahasa berorientasi objek. Kenali enam paradigma pemrograman yang didukung Java, lengkap dengan contoh sederhana."
---

## Pendahuluan

Saat orang mendengar kata **Java**, biasanya yang langsung terbayang adalah
*Object-Oriented Programming* (OOP). Padahal, Java sebenarnya adalah bahasa yang
bersifat **multi-paradigma** — artinya Java mendukung beberapa gaya menulis
program sekaligus.

**Paradigma pemrograman** sendiri adalah "cara pandang" atau gaya dalam menyusun
sebuah program. Sama seperti memasak: ada yang suka mengikuti resep langkah demi
langkah, ada yang lebih suka menyiapkan bahan dalam wadah-wadah rapi terlebih
dahulu. Tujuannya sama (masakan jadi), tapi pendekatannya berbeda.

Berikut enam paradigma yang didukung oleh Java.

## 1. Object-Oriented Programming (OOP)

Inilah paradigma utama Java. Di sini program dibangun dari **objek**, yaitu
gabungan antara data dan perilaku. OOP punya empat pilar: *inheritance*
(pewarisan), *encapsulation* (pembungkusan), dan *polymorphism* (perubahan bentuk)

> **Analogi:** Bayangkan sebuah cetakan kue (*class*) yang bisa dipakai membuat
> banyak kue (*object*). Setiap kue punya bentuk sama, tapi bisa diberi rasa
> berbeda.

```java
class Animal {
    void makeSound() {
        System.out.println("An animal makes a sound");
    }
}

class Cat extends Animal {   // pewarisan
    @Override
    void makeSound() {
        System.out.println("Meow!");
    }
}
```

## 2. Imperative Programming

Pada paradigma **imperatif**, kita memberi tahu komputer **langkah demi langkah**
apa yang harus dilakukan untuk mengubah keadaan (*state*) program. Biasanya
menggunakan perintah seperti perulangan dan percabangan.

> **Analogi:** Seperti memberi instruksi ke seseorang: "ambil gelas, isi air,
> lalu minum."

```java
int total = 0;
for (int i = 1; i <= 5; i++) {
    total = total + i;   // mengubah nilai 'total' langkah demi langkah
}
System.out.println(total); // 15
```

## 3. Procedural Programming

Paradigma **prosedural** adalah bagian dari gaya imperatif, tapi kodenya
dikelompokkan ke dalam **prosedur** atau **fungsi/metode**. Fungsi-fungsi inilah
yang mengolah data dan mengatur alur program.

> **Analogi:** Daripada menulis satu resep panjang, kita memecahnya menjadi
> beberapa langkah bernama, misalnya _function_ `add()`.

```java
static int add(int a, int b) {
    return a + b;
}

public static void main(String[] args) {
    System.out.println(add(3, 4)); // 7
}
```

## 4. Concurrent Programming

Java punya dukungan bawaan untuk **pemrograman konkuren**, yaitu menjalankan
beberapa tugas **secara bersamaan** (*multithreading*). Ini berguna agar program
tetap responsif, misalnya mengunduh file sambil tetap bisa diklik.

Java menyediakan banyak alat untuk ini lewat paket `java.util.concurrent`.

> **Analogi:** Seperti dapur dengan beberapa koki yang memasak berbagai pesanan
> di waktu yang sama, bukan satu koki yang mengerjakan semuanya berurutan.

```java
Thread thread = new Thread(() -> {
    System.out.println("Running in a separate thread");
});
thread.start();
```

## 5. Functional Programming

Mulai **Java 8**, Java mendukung **pemrograman fungsional**. Di sini kita
memperlakukan fungsi sebagai "warga kelas satu" — bisa disimpan dan dioper seperti
data biasa. Fiturnya antara lain *lambda expression*, *Stream API*, dan
*functional interface*.

> **Analogi:** Seperti memberi resep ke mesin otomatis, lalu mesin itu yang
> mengolah daftar bahan tanpa kita perlu mengatur perulangan secara manual.

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);

int evenSum = numbers.stream()
        .filter(n -> n % 2 == 0)   // ambil yang genap
        .mapToInt(n -> n)
        .sum();

System.out.println(evenSum); // 6
```

## 6. Generic Programming

Dengan **generics**, Java memungkinkan kita membuat *class* dan metode yang bisa
bekerja dengan **berbagai tipe data** secara aman. Tujuannya agar kode bisa
dipakai ulang tanpa mengorbankan keamanan tipe (*type safety*).

> **Analogi:** Seperti kotak penyimpanan yang bisa diberi label "khusus buku" atau
> "khusus sepatu". Isinya beda, tapi kotaknya sama dan kita tidak akan salah taruh.

```java
List<String> names = new ArrayList<>();   // hanya boleh String
names.add("Alice");
// names.add(123);  // <- ditolak saat kompilasi, aman!
```

## Ringkasan

| Paradigma | Inti Ide |
|---|---|
| **OOP** | Program dibangun dari objek (data + perilaku) |
| **Imperative** | Perintah langkah demi langkah untuk mengubah *state* |
| **Procedural** | Kode dikelompokkan dalam fungsi/prosedur |
| **Concurrent** | Menjalankan banyak tugas secara bersamaan |
| **Functional** | Fungsi sebagai data, pakai lambda & Stream |
| **Generic** | Kode aman untuk berbagai tipe data |

## Kesimpulan

Java sering dikenal sebagai bahasa OOP, padahal kemampuannya jauh lebih luas.
Dengan mendukung enam paradigma sekaligus — OOP, imperatif, prosedural, konkuren,
fungsional, dan generik — Java memberi kita kebebasan memilih gaya yang paling
cocok untuk setiap masalah.

Memahami berbagai paradigma ini membuat kita menjadi *programmer* yang lebih
fleksibel: tahu kapan harus memakai objek, kapan cukup dengan fungsi sederhana,
dan kapan memanfaatkan kekuatan pemrograman fungsional.
