## Tugas Individu - PBP C
### Deltakristiano Kurniaputra - NPM: 2406425810

<details><summary> Tugas 9: Integrasi Layanan Web Django dengan Aplikasi Flutter </summary>

## **Q1:** Jelaskan mengapa kita perlu membuat model Dart saat mengambil/mengirim data JSON? Apa konsekuensinya jika langsung memetakan `Map<String, dynamic>` tanpa model (terkait validasi tipe, null-safety, maintainability)?

Pertama, **dari segi keamanan tipe data**: Model Dart memastikan kalau data yang masuk atau keluar dari API itu benar-benar sesuai dengan tipe yang diharapkan. Bayangkan kita expect `age` berupa integer, tapi data JSON kirimnya string—tanpa model, ini bisa jadi error di runtime yang susah di-debug.

Kedua, **null-safety**: Dart punya fitur bagus untuk menangani null value. Dengan model, kita bisa eksplisit setin mana field yang boleh null dan mana yang wajib ada. Ini jauh lebih aman dibanding langsung pake `Map<String, dynamic>` yang bisa bikin app crash di tempat yang nggak terduga.

Ketiga, **kode lebih rapi dan mudah dirawat**: Model memberikan struktur yang jelas. Kalau kemudian ada developer lain (atau diri sendiri bulan depan) yang baca kode, dia langsung tau bentuk datanya apa. Kalau cuma map, kan susah untuk tracking field apa aja yang seharusnya ada.

Tambahan lagi, **developer experience jadi lebih enak**: IDE bisa kasih autocomplete dan suggestion yang akurat karena tau tipe-tipe fieldnya. Selain itu, model juga biasanya punya method bawaan untuk konversi dari/ke JSON, jadi nggak perlu manual mapping satu-satu yang ribet.

Dan terakhir, **data tetap konsisten**: Semua bagian app yang pake data itu akan handle data dengan cara yang sama karena pakai struktur model yang sama.

---

## **Q2:** Apa fungsi package http dan CookieRequest dalam tugas ini? Jelaskan perbedaan peran http vs CookieRequest.

**Package `http`** itu semacam alat dasar untuk bikin request HTTP—bisa GET, POST, PUT, DELETE. Cara kerjanya sederhana: kita kirim request, terus terima response. Tapi ada kelemahannya: `http` nggak auto-handle cookie atau session authentication. Kalau mau maintain session, kita kudu manage cookie secara manual, yang repot.

**CookieRequest** lebih spesifik—ini dari package `pbp_django_auth`. Dia dirancang khusus buat kerja sama sama Django dan authentication. Yang bagus: dia **otomatis** simpen dan kirim cookie untuk maintain session. Jadi kalau user login, CookieRequest bakal automatically attach cookie ke setiap request berikutnya tanpa kita suruh. Ini bikin proses login-logout-akses endpoint jadi jauh lebih smooth.

Singkatnya: `http` itu alat generik, CookieRequest itu specialist untuk Django auth. Kalau kita perlu session yang consistent, CookieRequest jauh lebih praktis.

---

## **Q3:** Jelaskan mengapa instance CookieRequest perlu untuk dibagikan ke semua komponen di aplikasi Flutter.

**Pertama, session tetap konsisten**: Kalau instance-nya dibagikan (biasanya pakai Provider), semua komponen punya akses ke sesi yang sama. Jadi kalau user udah login di halaman A, terus pindah ke halaman B, session-nya masih aktif. User nggak perlu login lagi.

**Kedua, efficient cookie management**: CookieRequest auto-manage cookie yang diperlukan. Kalau dibagikan satu instance, kita nggak perlu create ulang atau handle cookie di tiap komponen—lebih simple, lebih minim error.

**Ketiga, akses auth function lebih mudah**: Semua komponen bisa langsung pake function login, logout, check auth status tanpa perlu boilerplate code di mana-mana.

**Catatan penting**: CookieRequest itu stateful, artinya dia keep track dari state session. Kalau kita bikin instance baru di setiap screen, cookie bisa hilang dan session berantakan. Makanya harus satu instance yang dishare via Provider atau dependency injection lainnya.

---

## **Q4:** Jelaskan konfigurasi konektivitas yang diperlukan agar Flutter dapat berkomunikasi dengan Django. Mengapa kita perlu menambahkan 10.0.2.2 pada ALLOWED_HOSTS, mengaktifkan CORS dan pengaturan SameSite/cookie, dan menambahkan izin akses internet di Android? Apa yang akan terjadi jika konfigurasi tersebut tidak dilakukan dengan benar?

**Menambahkan `10.0.2.2` ke ALLOWED_HOSTS**: Ini IP khusus yang dipakai emulator Android untuk akses localhost dari developer machine. Jadi kalo nggak di-add ke ALLOWED_HOSTS di settings Django, Django bakal tolak request dari emulator. Contoh: kalo emulator minta dari `10.0.2.2:8000`, tapi `10.0.2.2` nggak ada di ALLOWED_HOSTS, response-nya error 400 Bad Request.

**CORS harus aktif**: CORS (Cross-Origin Resource Sharing) itu untuk izin request lintas domain/port. Flutter app biasanya jalan di port beda atau environment beda dari Django. Tanpa CORS, browser/app bakal block request tersebut. Kalo nggak setup, error yang keluar semacam "origin not allowed" atau request simply hang.

**Cookie configuration (SameSite dan Secure)**: Cookie auth Django (sessionid, csrftoken) harus bisa dikirim sama Flutter. Kalau `SameSite` setting salah atau `Secure` nggak di-set dengan tepat, cookie nggak akan attach ke request, sehingga auth gagal. Contoh: kalo kita set `SameSite=Strict`, mungkin cookie nggak akan dikirim di cross-origin request.

**Internet permission di Android**: Di file `AndroidManifest.xml`, kita harus add `<uses-permission android:name="android.permission.INTERNET"/>`. Ini basic permission buat akses internet. Tanpa ini, app nggak bisa connect ke server sama sekali—semua network call bakal fail.

**Apa yang terjadi kalo nggak benar?** Hasilnya bisa:
- App nggak bisa konek ke backend (timeout atau connection refused)
- Request di-block karena CORS error
- Login nggak work karena cookie nggak dikirim
- Network call total fail karena permission denied

---

## **Q5:** Jelaskan mekanisme pengiriman data mulai dari input hingga dapat ditampilkan pada Flutter.

**Step 1 - User input**: Pengguna isi form di Flutter, misalnya login form atau form tambah produk.

**Step 2 - Buat request HTTP**: Setelah submit, Flutter trigger `CookieRequest` untuk kirim request (biasanya POST) ke endpoint Django yang sesuai. Data dari form di-format jadi JSON dan di-attach ke request body.

**Step 3 - Django process**: Backend terima request, parse JSON, validate data, process sesuai logic (e.g., save ke database, cek username-password). Terus return response dalam JSON juga.

**Step 4 - Response dari Django**: Django kirim response balik—bisa berisi data yang diminta (list of products, user info) atau status (sukses/gagal). Response ini juga dalam format JSON.

**Step 5 - Flutter handle response**: Flutter terima JSON response, parse pakai model yang udah kita bikin, terus update state. Kalau data berisi list produk, Flutter tampilin di ListView atau GridView. Kalau response cuma status, Flutter tampilin snackbar atau toast ke user.

---

## **Q6:** Jelaskan mekanisme autentikasi dari login, register, hingga logout. Mulai dari input data akun pada Flutter ke Django hingga selesainya proses autentikasi oleh Django dan tampilnya menu pada Flutter.

**Register flow**:
- User isi form register (username, password, email) di Flutter
- Flutter kirim POST request ke Django endpoint register dengan data dalam JSON
- Django terima, validate (cek duplicate username, password strength), bikin user baru
- Django return response (sukses atau error message)
- Flutter display pesan ke user—kalo sukses, bisa redirect ke login; kalo error, tampilin alert

**Login flow**:
- User isi username & password, terus click login button
- Flutter kirim POST request ke endpoint login Django
- Django validate credentials, kalo cocok: bikin session dan return response dengan cookie (sessionid, csrftoken)
- CookieRequest otomatis store cookie ini—request berikutnya bakal auto-attach cookie
- Flutter terima response sukses, update UI (redirect ke home/dashboard, show menu)
- Kalau login gagal, Django kirim error response, Flutter tampilin pesan error

**Logout flow**:
- User click logout button
- Flutter kirim POST request ke endpoint logout Django
- Django destroy session di server, return response (biasanya just ok status)
- CookieRequest clear stored cookie
- Flutter clear local state/cache, redirect ke login page
- User effectively keluar dari app

---

## **Q7:** Jelaskan bagaimana cara kamu mengimplementasikan checklist di atas secara step-by-step! (bukan hanya sekadar mengikuti tutorial).

Ini langkah-langkah yang saya lakukan:

1. **Setup project**: Pastiin Django project berjalan, bisa diakses. Test endpoint dengan postman atau curl.

2. **Bikin model di Django**: Tentuin fields yang diperlukan (name, description, price, stock, dll). Migrate database.

3. **Buat endpoint API**: Setup endpoint return JSON—e.g., `/api/products/` return semua produk, `/api/products/<id>/` return product detail. Juga endpoint login, register, logout.

4. **Config CORS dan ALLOWED_HOSTS**: Add setting di Django settings.py untuk CORS dan add localhost/10.0.2.2 ke ALLOWED_HOSTS.

5. **Bikin model di Flutter**: Map Django model ke Dart class. Include semua field, terus bikin method `fromJson()` dan `toJson()`.

6. **Setup Provider + CookieRequest**: Wrap app dengan Provider untuk CookieRequest instance, sehingga semua screen bisa akses.

7. **Bikin page login & register**: UI sederhana, POST request pake CookieRequest, handle response (show error atau redirect).

8. **Bikin page list produk**: Fetch dari Django endpoint, parse JSON, tampilin dalam ListView/GridView. Add loading indicator selama fetch.

9. **Bikin page detail**: Get detail produk dari endpoint atau pass data dari list page, tampilin semua info. Add "back" button.

10. **Add filter feature**: Punya button/toggle untuk filter "My Products" vs "All Products"—ini bisa dilakukan client-side dengan filter list yang sudah ada.

11. **Bikin form tambah produk**: Form dengan validation, submit kirim data ke Django endpoint. Server handle save, return id produk atau confirmation.

12. **Polish UI**: Tambahin warna, spacing, icons. Pake Material 3 design system. Product card bisa punya badge (Featured, Stock status).

13. **Test**: Test flow login → list produk → detail produk → logout. Check kalau filter work, form validation work, error handling work.

14. **Push ke GitHub**: `git add .`, `git commit -m "..."`, `git push`.

---

## Checklist Tugas

- [X] Deployment Django project sudah jalan
- [X] Register account di Flutter
- [X] Login page
- [X] Integrasi auth Django ↔ Flutter
- [X] Model custom (sesuai Django model)
- [X] Halaman list item (dari JSON endpoint)
- [X] Display: name, price, description, thumbnail, category, is_featured, brand, rating, stock
- [X] Halaman detail per item
- [X] Detail page accessible dari card click
- [X] Display semua atribut di detail page (except timestamps)
- [X] Back button
- [X] Filter: hanya item milik user yang login (client-side filtering)
- [X] Jawab semua Q1-Q7 di README.md
- [X] Add → Commit → Push ke GitHub
- [X] **BONUS**: Form tambah produk dengan field lengkap
- [X] **BONUS**: UI/UX improvement dengan Material 3, modern design, product badges

</details>

<details><summary>Tugas 8: Flutter Navigation, Layouts, Forms, and Input Elements</summary>

## **Q1**: Jelaskan perbedaan antara `Navigator.push()` dan `Navigator.pushReplacement()` pada Flutter. Dalam kasus apa sebaiknya masing-masing digunakan pada aplikasi Football Shop kamu?

`Navigator.push()` berfungsi untuk menambahkan halaman baru ke dalam navigation stack sambil tetap menyimpan halaman sebelumnya. Dengan ini, pengguna masih bisa kembali ke halaman lama dengan tombol back. Sedangkan `Navigator.pushReplacement()` mengganti halaman aktif dengan halaman baru, sehingga halaman sebelumnya hilang dari stack dan pengguna tidak bisa kembali lagi.
Pada NextJersey `push()` cocok dipakai untuk kasus seperti saat pengguna membuka halaman detail produk dari list produk. Mereka bisa lihat detail lengkapnya dan kemudian kembali ke halaman utama kalau mau. Sementara `pushReplacement()` cocok saya gunakan saat proses login selesai - pengguna langsung ke dashboard dan tidak perlu bisa kembali ke halaman login lagi.

---

## **Q2:** Bagaimana kamu memanfaatkan hierarchy widget seperti Scaffold, AppBar, dan Drawer untuk membangun struktur halaman yang konsisten di seluruh aplikasi?

- **Scaffold**: Ini saya jadikan fondasi setiap halaman di aplikasi. Scaffold memberikan kerangka kerja standar yang sudah include area untuk app bar, navigasi drawer, dan konten utama. Jadi semua halaman punya susunan yang sama dan mudah dipahami pengguna.

- **AppBar**: Di setiap halaman, AppBar saya letakkan di atas untuk menampilkan judul dan fitur penting.

- **Drawer**: Saya buat drawer yang muncul dari sisi kiri dan berisi menu utama seperti Home dan add Product. Dengan drawer yang konsisten, pengguna selalu tahu bagaimana cara navigasi antar halaman tanpa perlu bingung.
  
---

## **Q3:** Dalam konteks desain antarmuka, apa kelebihan menggunakan layout widget seperti Padding, SingleChildScrollView, dan ListView saat menampilkan elemen-elemen form? Berikan contoh penggunaannya dari aplikasi kamu.

- **Padding**: Widget ini membantu saya memberikan spacing yang pas di setiap elemen form. Hasilnya, form terlihat tidak berantakan dan lebih nyaman dipandang. Saya pakai untuk memberi jarak di antara field kategori, price, dan deskripsi produk.

- **SingleChildScrollView**: Berguna banget untuk form yang panjang. Saya bungkus seluruh form dengan widget ini agar bisa di-scroll kalau kontennya melebihi ukuran layar. Jadi pengguna tidak bakal kebingungan mencari field yang tersembunyi di bawah.

- **ListView**: Widget ini saya gunakan di dalam `Drawer` untuk menampilkan navigation menu seperti "Home Page" dan "Add Products". Widget ini dapat menampilkan list atau daftar yang dapat discroll. Performanya bagus dan bisa handle banyak item tanpa lag.

---

## **Q4:** Bagaimana kamu menyesuaikan warna tema agar aplikasi Football Shop memiliki identitas visual yang konsisten dengan brand toko?

Saya memilih palet warna hitam dan merah sebagai identitas visual NextJersey yang konsisten di seluruh aplikasi:

- **Theme Global**: Di file `main.dart`, saya set `primarySwatch` ke `Colors.black` dan secondary color ke `Colors.red`. Ini otomatis diterapkan ke semua komponen widget.

- **AppBar dan Navigation**: Saya manfaatkan `Theme.of(context).colorScheme.primary` untuk AppBar dan Drawer agar warna-warnanya selalu selaras dengan tema utama.

- **Button dan Interactive Elements**: Tombol action seperti "Add to Cart" dan "Save" saya beri warna secondary (red) biar ada kontras visual dan menarik perhatian pengguna.

---
Checklist untuk tugas ini adalah sebagai berikut:
- [X] Membuat minimal satu halaman baru pada aplikasi, yaitu halaman formulir tambah produk baru dengan ketentuan sebagai berikut:
  - [X] Memakai minimal tiga elemen input, yaitu name, price, dan description.
  - [X] Tambahkan elemen input lain sesuai dengan model pada aplikasi Football Shop Django yang telah kamu buat (misalnya thumbnail, category, dan isFeatured).
- [X] Memiliki sebuah tombol Save.
- [X] Setiap elemen input di formulir juga harus divalidasi dengan ketentuan sebagai berikut:
  - [X] Setiap elemen input tidak boleh kosong.
  - [X] Setiap elemen input harus berisi data dengan tipe data atribut modelnya.
- [X] Mengarahkan pengguna ke halaman form tambah produk baru ketika menekan tombol Tambah Produk pada halaman utama.
- [X] Memunculkan data sesuai isi dari formulir yang diisi dalam sebuah pop-up setelah menekan tombol Save pada halaman form tambah produk baru.
- [X] Membuat sebuah drawer pada aplikasi dengan ketentuan sebagai berikut:
  - [X] Drawer minimal memiliki dua buah opsi, yaitu Halaman Utama dan Tambah Produk.
  - [X] Ketika memilih opsi Halaman Utama, aplikasi akan mengarahkan pengguna ke halaman utama.
  - [X] Ketika memilih opsi Tambah Produk, aplikasi akan mengarahkan pengguna ke halaman form tambah produk baru.
- [X] Menjawab beberapa pertanyaan berikut pada README.md pada root folder (silakan modifikasi README.md yang telah kamu buat sebelumnya dan tambahkan subjudul untuk setiap tugas):
  - [X] Jelaskan perbedaan antara `Navigator.push()` dan `Navigator.pushReplacement()` pada Flutter. Dalam kasus apa sebaiknya masing-masing digunakan pada aplikasi Football Shop kamu?
  - [X] Bagaimana kamu memanfaatkan hierarchy widget seperti Scaffold, AppBar, dan Drawer untuk membangun struktur halaman yang konsisten di seluruh aplikasi?
  - [X] Dalam konteks desain antarmuka, apa kelebihan menggunakan layout widget seperti Padding, SingleChildScrollView, dan ListView saat menampilkan elemen-elemen form? Berikan contoh penggunaannya dari aplikasi kamu.
  - [X] Bagaimana kamu menyesuaikan warna tema agar aplikasi Football Shop memiliki identitas visual yang konsisten dengan brand toko?
- [X] Melakukan add, commit, dan push ke GitHub.

</details>

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