---
title: "Kenapa Composition Lebih Disukai daripada Inheritance Dalam OOP?"
date: 2026-06-30
tags: ["java", "oop"]
description: "Dalam OOP ada prinsip terkenal: composition over inheritance. Kenapa komposisi sering lebih disukai daripada pewarisan? Mari kita bahas dengan sederhana."
---

## Pendahuluan 

Dalam pemrograman berorientasi objek (OOP), ada dua cara umum untuk menggunakan
kembali kode dari satu *class* di *class* lain: **pewarisan** (*inheritance*) dan
**komposisi** (*composition*).

Ada satu prinsip desain yang sangat terkenal: **"composition over inheritance"**
— utamakan komposisi daripada pewarisan. Kenapa begitu? Mari kita pahami dulu
perbedaan keduanya.

## Pewarisan: Hubungan "adalah" (is-a)

Pewarisan dipakai ketika sebuah *class* **adalah** jenis dari *class* lain.
Misalnya, `Cat` **adalah** `Animal`, jadi `Cat` mewarisi sifat `Animal`.

> **Analogi:** Anak mewarisi sifat dari orang tuanya. `extends` di Java berarti
> "menurunkan sifat".

```java
class Animal {
    void breathe() {
        System.out.println("Breathing...");
    }
}

class Cat extends Animal {   // Cat "is-a" Animal
    void meow() {
        System.out.println("Meow!");
    }
}
```

Masalah muncul ketika orang memaksakan pewarisan padahal hubungannya tidak benar.
Contohnya `Car extends Engine` — padahal mobil **bukan** mesin, melainkan
**punya** mesin.

## Komposisi: Hubungan "punya" (has-a)

Komposisi dipakai ketika sebuah *class* **memiliki** *class* lain sebagai bagian
di dalamnya. Mobil **punya** mesin, jadi objek `Engine` diletakkan di dalam
`Car`.

> **Analogi:** Sebuah komputer "punya" prosesor, RAM, dan SSD. Komputer tidak
> "mewarisi" prosesor — ia merakitnya dari komponen-komponen itu.

```java
class Engine {
    void start() {
        System.out.println("Engine started");
    }
}

class Car {
    private Engine engine = new Engine();   // Car "has-a" Engine

    void start() {
        engine.start();   // mendelegasikan tugas ke komponen
    }
}
```

## Kenapa Komposisi Lebih Disukai?

### 1. Fleksibilitas

Dengan komposisi, kita bisa **mengganti perilaku saat program berjalan**
(*runtime*) hanya dengan menukar komponennya. Contohnya, mobil bisa diberi mesin
bensin atau mesin listrik tanpa mengubah *class* `Car`.

```java
interface Engine {
    void start();
}

class PetrolEngine implements Engine {
    public void start() { System.out.println("Petrol engine roars"); }
}

class ElectricEngine implements Engine {
    public void start() { System.out.println("Electric engine hums"); }
}

class Car {
    private Engine engine;

    Car(Engine engine) {     // mesin ditentukan saat objek dibuat
        this.engine = engine;
    }

    void start() {
        engine.start();
    }
}

// Tinggal pilih mesinnya:
Car tesla = new Car(new ElectricEngine());
Car avanza = new Car(new PetrolEngine());
```

### 2. Keterikatan yang Longgar (Loose Coupling)

*Class* menjadi tidak terlalu bergantung pada detail isi *class* lain. `Car`
cukup tahu bahwa `Engine` punya metode `start()`, tanpa peduli bagaimana mesin
itu bekerja di dalamnya.

### 3. Menghindari Masalah Pewarisan

Pewarisan yang berlapis-lapis bisa menimbulkan masalah, salah satunya **diamond
problem** — kebingungan ketika sebuah *class* mewarisi dari dua "induk" yang
punya metode sama. Java bahkan **melarang** pewarisan banyak *class* sekaligus
untuk menghindari ini. Komposisi tidak punya masalah seperti itu.

### 4. Enkapsulasi yang Lebih Baik

Komposisi menyembunyikan komponen di dalam *class* dan hanya membuka bagian yang
benar-benar perlu. Pewarisan justru cenderung membuka "isi dalam" induk ke
turunannya, sehingga batasannya jadi kabur.

### 5. Penggunaan Ulang Kode

Kita bisa **merakit** berbagai komponen kecil untuk menghasilkan perilaku yang
beragam. Komponen yang sama (`Engine`, `GPS`, `Radio`) bisa dipakai ulang di
banyak *class* berbeda.

### 6. Lebih Mudah Dirawat

Selama "kontrak" (*interface*) komponen tidak berubah, perubahan di dalam komponen
tidak akan merusak *class* yang memakainya. Ini membuat perawatan kode jauh lebih
aman dibanding pewarisan yang saling terikat erat.

## Ringkasan

| Aspek | Pewarisan (*inheritance*) | Komposisi (*composition*) |
|---|---|---|
| Hubungan | "adalah" (is-a) | "punya" (has-a) |
| Keterikatan | Erat (*tight coupling*) | Longgar (*loose coupling*) |
| Ubah perilaku saat *runtime* | Sulit | Mudah (tukar komponen) |
| Risiko *diamond problem* | Ada | Tidak ada |
| Perawatan | Lebih rapuh | Lebih aman |

## Kesimpulan

Pewarisan bukanlah sesuatu yang buruk — ia tetap berguna saat hubungan
**"is-a"** benar-benar tepat. Namun untuk kebanyakan kasus, **komposisi** memberi
desain yang lebih fleksibel, longgar, dan mudah dirawat.

Aturan praktisnya: **gunakan komposisi sebagai pilihan utama**, dan pakai
pewarisan hanya ketika sebuah objek benar-benar **adalah** jenis dari objek lain.
