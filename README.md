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
1) Di kasus BambangShop, menurut saya, satu Model struct sudah cukup untuk sekarang. Hal ini karena subscriber di konteks ini hanya menyimpan data subscriber dan menjalankan behaviour yang sama untuk semua subscriber, yaitu menerima notifikasi. Hal ini sama dengan mengatakan bahwa ketika semua subsciber punya struct dan behaviour yang sama, interface tidak terlalu dibutuhkan karena kemungkinan adanya modifikasi cukup kecil.  
Di sisi lain, jika kita meninjau dari Observer Pattern secara konsep atau clean code principle pada dasarnya, interface akan tetap dibutuhkan untuk memenuhi principle di mana suatu code harus extendable (open to changes), di mana jika sewaktu-waktu kita ingin ada banyak subscriber dengan behaviour yang berbeda-beda, kita bisa langsung mengimplementasikannya lewat satu abstraction (dalam konteks ini trait) yang sama.

2) Jika hanya menggunakan Vec, hal ini akan menjadi kurang efisien untuk kasus ini. Dengan menggunakan Vec berarti pengecekan dilakukan secara manual dengan looping id atau url satu persatu. Hal ini sangat memberatkan sistem untuk alokasi yang tidak advanced yang sebenarnya dapat dialokasikan untuk fungsi yang lebih bermutu.  
Dengan menggunakan DashMap, akan lebih sesuai dengan konteks ini karena sistemnya melakukan pengecekan dengan menggunakan Key, dengan adanya Key, penjagaan id atau url tetap unik juga akan lebih mudah dilakukan, begitu juga dengan add, delete, dan search, karena kita tinggal mengakses berdasarkan key dan mendapatkan pasangannya.

3) Menurut saya, Singleton tidak bisa menggantikan fungsi DashMap, karena keduanya memiliki fungsi yang berbeda. Singleton hanya memastikan hanya ada satu instance dari sebuah objek, sedangkan DashMap digunakan untuk menyimpan data yang digunakan oleh banyak thread. Dengan hanya mengimplementasikan Singleton, kita belum memiliki akses data yang cepat, akses data dengan key, dan thread safety. Dalam konteks ini, akan lebih relevan untuk menggunakan DashMap karena kita juga mmebutuhkan storage yang aman dan efisien untuk banyak SUBSCRIBER, tidak bisa digantikan oleh Singleton, meskipun Singleton dapat digunakan untuk mewrap DashMap tersebut.
#### Reflection Publisher-2
1. Menurut saya, pemisahan antara Model, Service, dan Repository diperlukan karena masing-masing memiliki tanggung jawab yang berbeda. Kalau semua tanggung jawab tersebut digabungkan dalam satu Model, maka struktur kode akan menjadi terlalu padat dan sulit dimaintain.  
Repository harusnya berfokus pada pengelolaan data dan akses ke storage, sedangkan Service menghandle business logic yang lebih kompleks. Dengan memisahkan keduanya, code menjadi lebih modular dan setiap bagian punya peran yang jelas. Hal ini juga membuat perubahan pada logika bisnis atau penyimpanan data tidak harus memengaruhi seluruh bagian Model.

2. Jika hanya menggunakan Model, menurut saya code akan menjadi lebih rumit karena setiap model akan saling bergantung secara langsung. Program, Subscriber, dan Notification tidak hanya menyimpan data, tetapi juga harus menangani alur komunikasi dan logic tambahan.
Akibatnya, setiap model harus mengetahui terlalu banyak hal tentang model lain, sehingga complexity meningkat. Misalnya, Program harus mengatur subscriber sekaligus mengirim notifikasi, sementara Subscriber juga harus menghandle bagaimana notifikasi diterima. Hal ini membuat code menjadi lebih sulit dibaca dan sulit diuji, serta perubahan pada satu bagian dapat berdampak ke banyak bagian lain.

3. Menurut saya, Postman sangat membantu dalam proses testing karena memungkinkan kita untuk langsung mencoba endpoint tanpa perlu membuat frontend terlebih dahulu. Hal ini memudahkan dalam mengecek apakah request dan response sudah berjalan sesuai dengan yang diharapkan.
Fitur yang paling membantu adalah collection karena endpoint dapat dikelompokkan dan digunakan kembali dengan mudah. Selain itu, fitur seperti request body, headers, dan environment variables juga memudahkan dalam melakukan penyesuaian tanpa harus mengubah kode. Response viewer juga membantu dalam melihat hasil response dengan lebih jelas. Postman akan tetap berguna untuk project ke depan karena membuat proses testing API menjadi lebih cepat dan efisien.
#### Reflection Publisher-3
1. Di tutorial ini, kita menggunakan variasi Push model. Hal ini bis adilihat dari bagaimana NotificationService (sebagai publisher) membentuk data notifikasi (payload) dan langsung mengirimkannya ke URL masing-masing Subscriber melalui method update. Dalam hal ini, subscriber bersifat pasif dan hanya bertugas menerima data lengkap yang diberikan oleh publisher tanpa harus memintanya terlebih dahulu.

2. Jika kita membayangkan menggunakan variasi Pull model untuk kasus tutorial ini:
Keuntungan: Subscriber memiliki kontrol penuh kapan mereka mau mengambil data. Publisher cukup mengirimkan sinyal kecil dan subscriber bisa melakukan request untuk pull detail data tersebut hanya ketika resource mereka sedang siap. Hal ini mencegah subscriber menerima data besar yang mungkin tidak langsung mereka proses.
Kekurangan: Akan terjadi proses komunikasi bolak-balik yang tidak efisien (network overhead). Publisher harus mengirimkan notifikasi awal, lalu setiap subscriber harus menembak request balik (misalnya GET request) ke sistem kita untuk mengambil datanya. Kalau ada banyak subscriber yang menerima ping dan melakukan pull secara bersamaan, hal ini bisa membebani server kita mejadi bottleneck sampai semua subscriber mendapatkan data lengkapnya.

3. Jika kita memutuskan untuk tidak menggunakan multi-threading (seperti menghapus thread::spawn) dalam proses notifikasi, program akan mengeksekusi pengiriman notifikasi secara synchronous atau berurutan di dalam satu loop.
Akibatnya, publisher harus menunggu proses HTTP POST request ke satu URL subscriber selesai terlebih dahulu sebelum bisa lanjut mengirim ke subscriber berikutnya. Jika ada satu server subscriber yang sedang down atau sangat lambat merespon, seluruh antrean notifikasi untuk subscriber lain akan ikut tertahan. Hal ini akan membuat sistem notifikasi menjadi sangat lambat dan tidak scalable saat jumlah subscriber semakin banyak.