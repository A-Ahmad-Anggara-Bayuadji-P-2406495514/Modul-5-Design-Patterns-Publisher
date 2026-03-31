# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

**1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber
is defined as an interface. Explain based on your understanding of Observer design patterns,
do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model
struct is enough?**

Dalam konteks aplikasi BambangShop, penggunaan sebuah Model struct tunggal sebenarnya sudah cukup memadai jika kebutuhan notifikasi hanya terbatas pada satu jenis mekanisme pengiriman data. Namun, berdasarkan prinsip Observer design pattern, penggunaan interface atau trait dalam Rust tetap menjadi praktik terbaik untuk menjamin fleksibilitas sistem di masa depan. Dengan menggunakan trait, subjek dapat berinteraksi dengan berbagai jenis subscriber yang memiliki implementasi berbeda tanpa harus mengetahui detail internal masing-masing objek. Hal ini memungkinkan kita untuk menambah jenis subscriber baru, misalnya notifikasi melalui email atau SMS, tanpa perlu mengubah logika inti pada sisi publisher. Jadi, meskipun saat ini satu struct terlihat cukup, trait menyediakan abstraksi yang diperlukan untuk menjaga kode tetap scalable dan memenuhi prinsip Open-Closed Principle.

**2. id in Program and url in Subscriber is intended to be unique. Explain based on your
understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently
use is necessary for this case?**

Penggunaan DashMap jauh lebih krusial dibandingkan sekadar menggunakan Vec biasa karena berkaitan dengan efisiensi pencarian dan manajemen data yang unik. Mengingat variabel id pada Program dan url pada Subscriber bersifat unik, DashMap memungkinkan akses data dengan kompleksitas waktu rata-rata $O(1)$, yang jauh lebih cepat daripada pencarian linear $O(n)$ pada Vec. Selain itu, DashMap mempermudah proses pemutakhiran atau penghapusan data secara spesifik tanpa harus melakukan iterasi ke seluruh elemen list. Dalam skenario di mana jumlah pengguna atau produk meningkat pesat, perbedaan performa ini akan sangat terasa bagi responsivitas aplikasi. Oleh karena itu, penggunaan hash map yang teroptimasi seperti DashMap adalah pilihan arsitektur yang sangat tepat untuk menangani data dengan identitas unik.

**3. When programming using Rust, we are enforced by rigorous compiler constraints to make a
thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we
used the DashMap external library for thread safe HashMap. Explain based on your
understanding of design patterns, do we still need DashMap or we can implement Singleton
pattern instead?**

Dalam ekosistem Rust, manajemen akses data global yang dapat dimutasi (mutable global state) sangat dibatasi oleh aturan borrow checker untuk mencegah data race. Meskipun kita dapat mengimplementasikan pola Singleton secara manual, pola tersebut tidak secara otomatis menjamin keamanan akses saat aplikasi dijalankan dalam lingkungan multi-threaded. DashMap menyediakan mekanisme sharded locking internal yang memungkinkan beberapa thread mengakses bagian map yang berbeda secara bersamaan tanpa saling mengunci seluruh struktur data. Jika kita hanya menggunakan Singleton konvensional tanpa bantuan pustaka yang mendukung concurrent access, kita akan dipaksa menggunakan global lock yang sangat berat dan dapat menurunkan performa aplikasi secara signifikan. Dengan demikian, DashMap bukan sekadar pengganti Singleton, melainkan solusi esensial untuk mencapai konkurensi yang aman dan efisien di Rust.

#### Reflection Publisher-2

**1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”.
Model in MVC covers both data storage and business logic. Explain based on your
understanding of design principles, why we need to separate “Service” and “Repository” from
a Model?**

Pemisahan antara Service dan Repository dari Model bertujuan untuk menerapkan prinsip Separation of Concerns agar kode lebih mudah dikelola dan diuji. Model seharusnya hanya merepresentasikan struktur data atau entitas bisnis murni, sementara Service bertugas menangani alur logika bisnis yang kompleks dan Repository fokus pada abstraksi akses penyimpanan data. Jika semua fungsi ini digabungkan ke dalam Model, maka objek tersebut akan menjadi terlalu besar dan sulit untuk dimodifikasi tanpa merusak bagian lainnya. Arsitektur berlapis ini memungkinkan kita untuk mengganti mekanisme penyimpanan data di Repository tanpa mengganggu logika bisnis di Service. Hal ini menciptakan struktur kode yang lebih modular, di mana setiap komponen memiliki tanggung jawab yang spesifik dan terisolasi.

**2. What happens if we only use the Model? Explain your imagination on how the interactions
between each model (Program, Subscriber, Notification) affect the code complexity for
each model?**

Jika kita hanya mengandalkan Model untuk menangani semua interaksi, kompleksitas kode akan meningkat secara eksponensial karena terjadinya ketergantungan yang sangat erat antar-entitas (tight coupling). Bayangkan jika model Program harus memahami cara mengirim notifikasi dan cara menyimpan data pengguna secara langsung; hal ini akan menciptakan dependensi sirkular yang sulit untuk didebug. Logika notifikasi dalam model Subscriber akan tercampur dengan logika validasi produk dalam model Notification, sehingga perubahan kecil pada satu bagian dapat merusak seluruh sistem. Kode akan menjadi sangat sulit untuk dilakukan unit testing karena tidak ada batasan yang jelas mengenai tanggung jawab masing-masing bagian. Pada akhirnya, seiring bertambahnya fitur, kode akan menjadi "spaghetti" yang sangat rentan terhadap kesalahan manusia.

**3. Have you explored more about Postman? Tell us how this tool helps you to test your current
work. You might want to also list which features in Postman you are interested in or feel like it
is helpful to help your Group Project or any of your future software engineering projects.**

Postman sangat penting bagi pengembang perangkat lunak karena mempermudah pengujian endpoint API secara mandiri tanpa harus membangun interface pengguna terlebih dahulu. Fitur Collections sangat membantu saya dalam mengelompokkan berbagai permintaan HTTP berdasarkan modul, sehingga proses pengujian berulang menjadi lebih terorganisir. Saya sangat tertarik dengan fitur Environment Variables yang memungkinkan kita mengganti basis URL atau token autentikasi secara otomatis saat berpindah dari lingkungan development ke production. Selain itu, kemampuan Postman untuk melakukan otomatisasi tes sederhana melalui skrip JavaScript sangat berguna untuk memastikan respons API sesuai dengan kontrak data yang diharapkan. Alat ini pastinya akan menjadi aset berharga bagi proyek kelompok saya untuk menjamin integrasi antar-layanan berjalan dengan lancar.
#### Reflection Publisher-3

**1. Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and
Pull model (subscribers pull data from publisher). In this tutorial case, which variation of
Observer Pattern that we use?**

Dalam kasus tutorial ini, variasi Observer Pattern yang kita gunakan adalah Push Model karena publisher secara aktif mengirimkan data notifikasi kepada para subscriber segera setelah terjadi perubahan status. Begitu sebuah produk baru dibuat, dihapus, atau dipromosikan, Main App akan langsung memicu fungsi notifikasi untuk memperbarui setiap Receiver App yang terdaftar. Pendekatan ini memastikan bahwa para pengamat mendapatkan informasi secara real-time tanpa perlu menanyakan status secara berkala ke server. Hal ini sangat efektif untuk skenario notifikasi di mana data harus segera sampai ke tangan pengguna tanpa penundaan. Dengan demikian, publisher memegang kendali penuh atas penyebaran informasi kepada seluruh pihak yang berkepentingan.

**2. What are the advantages and disadvantages of using the other variation of Observer Pattern
for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)**

Jika kita menggunakan Pull Model, maka setiap subscriber atau Receiver App harus melakukan permintaan (polling) secara rutin kepada Main App untuk mengecek apakah ada notifikasi baru. Keuntungan dari model ini adalah beban kerja publisher menjadi lebih ringan karena tidak perlu mengelola daftar alamat pelanggan atau mengirim data secara masif dalam satu waktu. Namun, kelemahannya adalah efisiensi jaringan akan menurun drastis karena banyaknya permintaan kosong yang dilakukan oleh subscriber saat tidak ada pembaruan data. Selain itu, pengguna mungkin tidak akan menerima informasi secara instan karena ada selisih waktu antara kemunculan data baru dan jadwal polling berikutnya. Untuk kasus toko daring seperti BambangShop, Push Model tetap jauh lebih unggul dalam memberikan pengalaman pengguna yang responsif.

**3. Explain what will happen to the program if we decide to not use multi-threading in the
notification process.
**

Tanpa penggunaan multi-threading, proses pengiriman notifikasi akan berjalan secara sinkron atau berurutan yang dapat menyebabkan bottleneck pada aplikasi utama. Program akan terhenti sejenak (freezing) untuk menunggu satu permintaan HTTP notifikasi selesai dikirim ke satu pelanggan sebelum melanjutkan ke pelanggan berikutnya. Jika terdapat ratusan atau ribuan pelanggan dengan koneksi internet yang lambat, maka Main App akan mengalami latensi yang sangat tinggi bahkan hingga gagal merespons permintaan pengguna lainnya. Hal ini sangat merugikan performa sistem secara keseluruhan karena operasi I/O yang berat menghambat eksekusi logika bisnis utama. Oleh karena itu, multi-threading sangat krusial untuk memastikan bahwa tugas pengiriman notifikasi berjalan di latar belakang tanpa mengganggu aliran utama program.
