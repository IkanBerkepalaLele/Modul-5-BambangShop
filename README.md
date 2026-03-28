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
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
- Berdasarkan source code dan deskripsi soal, struct sudah cukup untuk mengaplikasikan Observer Pattern, karena pada deskripsi soal, pak bambang hanya ingin notify lewat http request ke /receive, jadi semisalnya pak bambang ingin send request ke aplikasi yang berbeda, tinggal ganti URL saja. 

- Menurut saya vector masih bisa dipakai pada aplikasi ini, namun vector tidak threadsafe sehingga harus diwrap dengan mutex, jadi semisal ada produk baru dan ingin notify ke semua subscriber, tidak bisa dilakukan secara bersamaan dan harus mengantri sehingga terjadi bottleneck sehingga lebih lama, beda dengan dashmap yang thread safe sehingga bisa langsung dilakukan secara bersamaan dan lebih cepat. Jadi vector masih bisa dipakai untuk userbase kecil, namun tetap lebih baik pakai dashmap.

- Menurut pemahaman saya, singleton masih bisa dipakai, namun seperti pertanyaan 2, list of subscriber mau itu hashmap atau vector singleton tersebut harus diwrap menggunakan mutex, sehingga menjadi threadsafe walaupun lebih lambat. 

#### Reflection Publisher-2
- Menurut saya, pemisahan ini berhubungan denan prinsip SOLID, terutama SRP dan DIP, dimana untuk SRP, setiap modul punya fungsi spesifiknya masing2, model untuk struktur data, repository untuk akses, dan service untuk proses atau  logika bisnis. Untuk DIP, dimana setiap modul tidak boleh mengakses modul yang lebih rendah dan harus depend dengan sebuah abstraksi yang dimana tidak bisa dilakukan kalau modul masih menjadi 1. Jadi dengan dipisahnya service dan repo dari model, source code menjadi lebih modular dan rapi.

- Kalau aplikasi hanya menggunakan model, maka setiap modul akan jadi palugada karena harus mengemban logika bisnis dan teknis, satu modul juga bisa menyentuk modul lain yang membuat aplikasi menjadi tight coupled. Contohnya Product harus bisa kirim notif, cari subscriber, Notification harus kirim ke setiap subscriber, Subscriber harus tau product yang ada, yang membuat aplikasi memiliki circular dependency dan sulit dilakukan TDD. 

- Postman bisa saya gunakan untuk test alur dan test setiap api yang ada tanpa cek manual ke websitenya, lalu juga bisa cek api tersebut akan return apa. Untuk TK, mungkin yang bisa saya pakai nanti adalah collection runner untuk test apakah ada api yang rusak saat penambahan fitur dan scripting yang bisa saya gunakan agar test tadi bisa jadi lebih deskriptif dan otomatis.

#### Reflection Publisher-3
- Observer disini memakai push model, karena setiap ada perubahan, aplikasi akan push notifikasi ke receiver subscriber.

- Kalau observer memakai pull, maka aplikasi akan menyimpan perubahan yang ada, namun subscriber harus melakukan request ke server untuk pull perubahan yang telah dilakukan. Kelebihannya subscriber jadi bebas mau tahu kapan ada product yang baru. Tapi kekurangannya server bisa jadi keberatan karena bisa jadi ada request berulang kali walaupun tidak ada update.

- Program akan lebih lama, karena hanya ada 1 thread, maka proses pengiriman notifikasi akan dilakukan secara sekuensial namun tidak secara bersamaan.
