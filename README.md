## Tugas Individu - PBP C
### Deltakristiano Kurniaputra - NPM: 2406425810

<details><summary>Tugas 7: Elemen Dasar Flutter</summary>


## **Q1:** Jelaskan apa itu widget tree pada Flutter dan bagaimana hubungan parent-child (induk-anak) bekerja antar widget.

Widget tree pada Flutter basically adalah struktur yang menunjukkan bagaimana widget-widget di app terhubung satu sama lain. Jadi setiap widget bisa punya satu atau lebih widget anak (child) yang ada di dalamnya, dan itu yang namanya hubungan parent-child. Parent widget bertanggung jawab untuk mengatur layout dan behavior dari child widgets-nya. Contohnya, kalo kita pakai `Row` widget sebagai parent-nya, lalu kita menampung beberapa `Button` widget sebagai child-nya, nah `Row` itu yang bakal mengatur bagaimana `Button` widgets tadi ditampilkan secara horizontal.


---

## **Q2:** Sebutkan semua widget yang kamu gunakan dalam proyek ini dan jelaskan fungsinya.

1. `MaterialApp`: Widget utama yang membungkus seluruh aplikasi dan menyediakan konfigurasi tema serta sistem navigasi.
2. `Scaffold`: Widget yang memberikan struktur dasar untuk setiap halaman, mencakup AppBar, Body, dan FloatingActionButton.
3. `AppBar`: Bilah header yang ditampilkan di bagian atas layar, berfungsi untuk menampilkan judul dan action aplikasi.
4. `Column`: Widget untuk menampilkan widget anak (child) secara bertumpuk ke bawah (vertikal).
5. `Row`: Widget untuk menampilkan widget anak secara berdampingan ke samping (horizontal).
6. `Text`: Widget dasar untuk menampilkan teks di layar.
7. `Card`: Widget yang membuat elemen kartu dengan shadow, digunakan untuk menampilkan data seperti NPM, Nama, dan Kelas.
8. `GridView`: Widget untuk mengatur item dalam format grid, di sini saya gunakan dengan 3 kolom untuk tata letak yang rapi.
9. `Material`: Widget yang memberikan efek Material Design pada ItemCard agar terlihat lebih polished.
10. `InkWell`: Widget untuk menambahkan efek ripple ketika kartu ditekan dan menangani interaksi tap dari user.
11. `Container`: Widget untuk mengatur spacing dan layout konten di dalam kartu dengan padding yang sesuai.
12. `Icon`: Widget untuk menampilkan icon pada setiap tombol atau elemen interaktif.
13. `SnackBar`: Widget untuk menampilkan notifikasi pesan singkat di bagian bawah layar sebagai feedback saat user melakukan aksi.

---

## **Q3:** Apa fungsi dari widget MaterialApp? Jelaskan mengapa widget ini sering digunakan sebagai widget root.

Widget `MaterialApp` basically adalah fondasi aplikasi Flutter yang menerapkan Material Design dari Google. Widget ini sering dijadikan widget root karena menyediakan fitur-fitur essential yang dibutuhkan aplikasi, antara lain:

1. **Tema:** `MaterialApp` memungkinkan kita untuk mengatur tema aplikasi secara global, mulai dari palet warna, tipografi, hingga styling komponen lainnya.
2. **Navigasi:** Widget ini menyediakan sistem routing yang memudahkan kita untuk mengelola perpindahan antar halaman dan transisi yang smooth.
3. **Localization:** `MaterialApp` mendukung fitur lokalisasi sehingga aplikasi dapat menampilkan konten dalam berbagai bahasa sesuai preferensi user.
4. **Builder Function:** Widget ini menyediakan `builder` yang memungkinkan kita untuk membangun UI secara dinamis berdasarkan state aplikasi pada saat itu.

---

## **Q4:** Jelaskan perbedaan antara StatelessWidget dan StatefulWidget. Kapan kamu memilih salah satunya?

Perbedaan mendasar antara `StatelessWidget` dan `StatefulWidget` terletak pada ada atau tidaknya state yang bisa berubah selama widget tersebut berjalan.

- `StatelessWidget`: Widget ini tidak punya state yang dapat diubah, jadi tampilannya akan tetap sama sepanjang siklus hidupnya. Kita pakai `StatelessWidget` ketika kita yakin bahwa widget tersebut bersifat statis dan nggak perlu update berdasarkan interaksi user.

- `StatefulWidget`: Widget ini punya state yang bisa berubah-ubah selama widget masih hidup, sehingga tampilannya bisa di-update sesuai dengan perubahan state tersebut. Kita pakai `StatefulWidget` ketika kita memerlukan widget yang responsif terhadap interaksi user, perubahan data real-time, atau perubahan tampilan yang bersifat dinamis.

---

## **Q5:** Apa itu BuildContext dan mengapa penting di Flutter? Bagaimana penggunaannya di metode build?

`BuildContext` basically adalah objek yang merepresentasikan posisi widget dalam widget tree. `BuildContext` itu penting di Flutter karena dia memberikan akses ke informasi tentang widget yang ada di sekitarnya, seperti tema aplikasi, ukuran layar, dan orientasi device. Selain itu, `BuildContext` juga digunakan untuk melakukan navigasi antar halaman dan mengakses provider atau state management yang berada di level yang lebih tinggi dalam tree.

Dalam penggunaan praktis di metode `build`, `BuildContext` dipakai ketika kita perlu mengambil data atau trigger action dari parent widget atau service yang lebih tinggi. Contohnya, kita bisa menggunakan `BuildContext` untuk mengambil tema dengan `Theme.of(context)`, atau untuk menampilkan snackbar dengan `ScaffoldMessenger.of(context).showSnackBar()`, atau bahkan untuk navigasi halaman dengan `Navigator.push(context, route)`.

---

## **Q6:** Jelaskan konsep "hot reload" di Flutter dan bagaimana bedanya dengan "hot restart".

"Hot reload" adalah fitur di Flutter yang memungkinkan kita melihat perubahan kode secara langsung tanpa perlu restart aplikasi sepenuhnya. Saat kita melakukan hot reload, Flutter inject kode yang sudah diubah ke dalam aplikasi yang sedang running, dan yang penting adalah state aplikasi tetap terjaga. Fitur ini sangat membantu dalam mempercepat development cycle dan membuat proses debugging jadi lebih efisien.

Sementara itu, "hot restart" adalah proses yang lebih berat, di mana aplikasi akan restart dari nol. Semua state akan direset dan aplikasi akan reload semua kode dari awal lagi. Kita biasanya pakai hot restart ketika perubahan kode menyentuh struktur aplikasi secara fundamental, atau ketika kita ingin memastikan bahwa setiap perubahan benar-benar ter-apply dengan sempurna dan nggak ada state lama yang tertinggal.
---

Checklist untuk tugas ini adalah sebagai berikut:
- [X] Membuat sebuah program Flutter baru dengan tema Football shop yang sesuai dengan tugas-tugas sebelumnya.
- [X] Membuat tiga tombol sederhana dengan ikon dan teks untuk product kamu:
  - [X] All Products
  - [X] My Products
  - [X] Create Product
- [X] Mengimplementasikan warna-warna yang berbeda untuk setiap tombol:
  - [X] Warna biru untuk tombol All Products
  - [X] Warna hijau untuk tombol My Products
  - [X] Warna merah untuk tombol Create Product
- [X] Memunculkan Snackbar dengan tulisan:
  - [X] "Kamu telah menekan tombol All Products" ketika tombol All Products ditekan.
  - [X] "Kamu telah menekan tombol My Products" ketika tombol My Products ditekan.
  - [X] "Kamu telah menekan tombol Create Product" ketika tombol Create Product ditekan.
- [X] Jawab pertanyaan-pertanyaan berikut di file README.md pada folder root:
  - [X] Jelaskan apa itu widget tree pada Flutter dan bagaimana hubungan parent-child (induk-anak) bekerja antar widget.
  - [X] Sebutkan semua widget yang kamu gunakan dalam proyek ini dan jelaskan fungsinya.
  - [X] Apa fungsi dari widget MaterialApp? Jelaskan mengapa widget ini sering digunakan sebagai widget root.
  - [X] Jelaskan perbedaan antara StatelessWidget dan StatefulWidget. Kapan kamu memilih salah satunya?
  - [X] Apa itu BuildContext dan mengapa penting di Flutter? Bagaimana penggunaannya di metode build?
  - [X] Jelaskan konsep "hot reload" di Flutter dan bagaimana bedanya dengan "hot restart".
- [X] Melakukan add-commit-push ke suatu repositori baru di GitHub.

</details>