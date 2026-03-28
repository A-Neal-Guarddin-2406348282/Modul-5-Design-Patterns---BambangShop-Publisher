# BambangShop Publisher App by Neal Guarddin (2406348282)
Neal already followed all tutorials procedures and it all work well.

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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
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

1. **Apakah masih perlu interface/trait untuk `Subscriber`, atau cukup satu `struct` saja?**  
   Menurut saya, untuk kasus BambangShop ini **satu `struct` saja sudah cukup**. Alasannya, saat ini semua subscriber punya bentuk data yang sama, yaitu `url` dan `name`, lalu perilakunya juga sama: menerima notifikasi. Jadi kalau belum ada variasi perilaku yang berbeda-beda, membuat `trait` sebenarnya belum terlalu perlu. (aka belum terlalu butuh `interface`)  
   `trait` akan lebih berguna kalau nanti ada beberapa jenis subscriber dengan cara kerja berbeda, misalnya ada subscriber yang menerima notifikasi lewat HTTP, ada yang lewat email, atau ada yang menyimpan notifikasi ke database. Kalau kondisinya masih sederhana, `struct` lebih mudah dipahami dan lebih ringan (aka `concrete` class).

2. **Apakah `Vec` cukup, atau perlu `DashMap` karena `id` dan `url` harus unik?**  
   Untuk kasus ini, **`DashMap` lebih cocok** daripada `Vec`. Walaupun `Vec` bisa dipakai untuk menyimpan data, pencarian berdasarkan `id` atau `url` akan menjadi lambat karena harus dicek satu per satu. Selain itu, memastikan data unik di `Vec` juga lebih ribet, karena kita harus manual memeriksa apakah `id` atau `url` sudah ada.  
   Dengan `DashMap` (konsep `Map`), kita bisa langsung menyimpan data berdasarkan key, misalnya `id` untuk program atau `url` untuk subscriber. Jadi akses data lebih cepat dan struktur datanya lebih sesuai untuk kebutuhan repository. Jadi, untuk kasus ini `DashMap` lebih tepat dibanding `Vec`.

3. **Apakah masih perlu `DashMap`, atau cukup implement Singleton saja?**  
   Menurut saya, **masih perlu `DashMap`**. Singleton dan `DashMap` sebenarnya menyelesaikan masalah yang berbeda.  
   - **Singleton** dipakai supaya hanya ada satu instance database atau storage yang digunakan bersama.  
   - **DashMap** dipakai supaya data di dalam storage itu aman diakses secara bersamaan oleh banyak thread.  

   Jadi, kalau hanya memakai Singleton tanpa `DashMap`, kita memang punya satu data global, tetapi belum tentu aman kalau ada akses bersamaan dari beberapa request.
   Kesimpulannya, untuk kasus ini **Singleton saja tidak cukup**. Kita tetap butuh `DashMap` agar akses data aman dan sesuai dengan kebutuhan program concurrent di Rust.


#### Reflection Publisher-2

1. **Kenapa `Service` dan `Repository` perlu dipisah dari `Model`?**  
   Menurut saya, pemisahan ini penting supaya kode lebih rapi dan sesuai dengan tugas masing-masing.  
   - **Model** sebaiknya hanya berisi bentuk data, misalnya [`crate::model::product::Product`](src/model/product.rs), [`crate::model::subscriber::Subscriber`](src/model/subscriber.rs), dan [`crate::model::notification::Notification`](src/model/notification.rs).  
   - **Repository** seperti [`crate::repository::subscriber::SubscriberRepository`](src/repository/subscriber.rs) dipakai untuk urusan data storage, misalnya simpan, ambil, dan hapus data.  
   - **Service** seperti [`crate::service::notification::NotificationService`](src/service/notification.rs) dipakai untuk business logic, misalnya aturan subscribe, unsubscribe, dan proses notifikasi.  

   Kalau semua digabung ke Model, kode akan jadi “gemuk” dan campur aduk serta lebih mudah dimaintain. Dengan dipisah, kode lebih mudah dibaca, lebih gampang diuji, dan kalau ada perubahan kita tidak perlu bongkar semuanya.

2. **Apa yang terjadi kalau kita hanya memakai `Model` saja?**  
   Kalau hanya memakai Model, maka Model akan menanggung terlalu banyak tugas: menyimpan data, menjalankan logika bisnis, bahkan mungkin mengurus komunikasi antar objek. Akhirnya Model jadi terlalu kompleks.  

   Dalam kasus BambangShop, hubungan antara `Program`, `Subscriber`, dan `Notification` bisa membuat Model saling bergantung satu sama lain. Misalnya:
   - `Product` harus tahu kapan harus memanggil notifikasi,
   - `Subscriber` harus tahu cara menerima request HTTP,
   - `Notification` harus tahu isi pesan yang dikirim.

   Kalau semua masuk ke Model, kode jadi lebih sulit dipahami dan lebih susah dimaintain. Perubahan kecil di satu bagian bisa memengaruhi banyak bagian lain. Jadi, pemisahan `Service` dan `Repository` membantu agar tanggung jawab tiap bagian tetap jelas. At least ini pemisahan simpel sebelum menggunakan prinsip S.O.L.I.D.

3. **Apa manfaat Postman untuk menguji pekerjaan ini?**  
   Postman sangat membantu karena kita bisa mencoba endpoint API tanpa harus membuat frontend dulu. Jadi, kita bisa langsung cek apakah fitur seperti subscribe dan unsubscribe sudah jalan atau belum.  

   Contohnya, saya bisa mengirim request ke endpoint subscribe lewat [src/controller/mod.rs](src/controller/mod.rs) yang sudah me-mount route notification, lalu melihat apakah response-nya sudah sesuai. Dari screenshot contoh, request `POST /notification/subscribe/<product_type>` bisa langsung dites dengan body JSON. Itu memudahkan untuk memastikan:
   - request sudah diterima,
   - data subscriber tersimpan,
   - response status sudah benar,
   - format JSON sudah sesuai.

   Fitur Postman yang menurut saya paling berguna:
   - **Collection** untuk menyimpan banyak endpoint (muncul banyak JSON yang kita import)
   - **Body JSON** untuk kirim data request (testing data, entah itu Notification/Subscribe atau Product/List All Products),
   - **Headers** untuk atur `Content-Type` (biasanya lebih bagus untuk raw JSON),

   Buat proyek kelompok atau proyek software engineering ke depan, Postman membantu banget karena testing API jadi lebih cepat, rapi, dan gampang diulang.


#### Reflection Publisher-3
