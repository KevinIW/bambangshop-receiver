# BambangShop Receiver App
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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

1. In this tutorial, we used RwLock<> to synchronise the use of Vec of Notifications. Explain why it is necessary for this case, and explain why we don’t use Mutex<> instead?

Dalam tutorial ini, RwLock<> digunakan untuk menyinkronkan penggunaan Vec of Notifications karena menyediakan mekanisme untuk beberapa pembaca atau satu penulis pada suatu waktu. Hal ini diperlukan karena beberapa komponen (misalnya, publishers, subscribers) dapat mengakses Vec of Notifications secara bersamaan untuk tujuan membaca, seperti mengulangi daftar untuk mengirimkan pemberitahuan atau memeriksa pemberitahuan yang tertunda. RwLock memungkinkan beberapa pembaca untuk mengakses data secara bersamaan tanpa saling menghalangi, yang meningkatkan konkurensi dan kinerja dalam skenario di mana operasi pembacaan sering terjadi.

Alasan mengapa Mutex<> tidak digunakan sebagai pengganti RwLock<> adalah karena Mutex menerapkan akses eksklusif ke data, artinya hanya satu thread yang dapat memperoleh kunci pada satu waktu, baik itu untuk membaca atau menulis. Dalam konteks tutorial ini, di mana bisa terjadi banyak operasi pembacaan secara konkuren (misalnya, memeriksa pemberitahuan yang tertunda), penggunaan Mutex akan memperkenalkan persaingan yang tidak perlu dan berpotensi menurunkan kinerja. RwLock<> menyediakan solusi yang lebih sesuai dengan memungkinkan beberapa pembaca untuk mengakses data secara konkuren sambil tetap memastikan akses eksklusif untuk operasi penulisan, sehingga lebih sesuai dengan persyaratan aplikasi.

2. In this tutorial, we used lazy_static external library to define Vec and DashMap as a “static” variable. Compared to Java where we can mutate the content of a static variable via a static function, why didn't Rust allow us to do so?

Dalam tutorial ini, kita menggunakan library eksternal lazy_static untuk mendefinisikan Vec dan DashMap sebagai variabel "static". Berbeda dengan Java, di mana kita dapat memutasi isi dari variabel static melalui sebuah fungsi static, Rust tidak mengizinkan kita melakukannya secara langsung.

Alasannya adalah karena sistem kepemilikan dan peminjaman Rust, yang memastikan keamanan memori dan mencegah balapan data dengan menerapkan aturan ketat seputar aliasing yang dapat dimutasi. Di Rust, variabel static umumnya bersifat tidak dapat diubah (immutable), dan mengizinkan akses yang dapat dimutasi kepadanya dapat berpotensi menyebabkan balapan data atau perilaku yang tidak aman lainnya.

Sebagai gantinya, Rust mendorong penggunaan primitif konkurensi seperti mutex atau RwLock untuk mengakses dan memutasi data bersama dengan aman. Dengan menggunakan mekanisme sinkronisasi seperti lazy_static dalam kombinasi dengan mutex atau RwLock, Rust memastikan bahwa data bersama dapat diakses dan dimutasi secara aman dalam lingkungan konkuren sambil tetap mematuhi jaminan keamanannya. Pendekatan ini membantu mencegah bug konkurensi umum dan memastikan keandalan dan kebenaran program Rust, bahkan dalam skenario multi-threaded.
#### Reflection Subscriber-2
1. Have you explored things outside of the steps in the tutorial, for example: src/lib.rs? If not, explain why you didn’t do so. If yes, explain things that you’ve learned from those other parts of code.

Dalam proyek-proyek Rust, lib.rs berfungsi sebagai titik masuk untuk pustaka-pustaka. Ini adalah file sumber utama di mana Anda mendefinisikan modul-modul, tipe data, fungsi, dan item-item lain yang dimaksudkan untuk digunakan oleh bagian lain dari kode atau oleh proyek-proyek eksternal.

Ketika membuat proyek pustaka Rust baru menggunakan Cargo, lib.rs secara otomatis dibuat di dalam direktori src. File ini bertindak sebagai akar dari kumpulan pustaka dan memberikan lokasi pusat untuk mengatur dan membangun struktur kode.

Di dalam lib.rs, biasanya didefinisikan API publik dari pustaka dengan mendeklarasikan modul-modul dan item-item menggunakan kata kunci pub. Item-item publik ini dapat diakses oleh pengguna pustaka, memungkinkan mereka untuk menggunakan dan berinteraksi dengan fungsionalitas yang disediakan.

Selain itu, lib.rs juga dapat berisi modul-modul dan item-item pribadi yang tidak terpapar kepada pengguna eksternal dari pustaka. Item-item pribadi ini dapat digunakan secara internal untuk detail implementasi atau tujuan organisasi, memastikan bahwa hanya bagian-bagian yang dimaksud dari pustaka yang terlihat oleh kode eksternal.

Secara keseluruhan, lib.rs memainkan peran penting dalam proyek-proyek pustaka Rust dengan mendefinisikan antarmuka publik dari pustaka dan berfungsi sebagai titik masuk utama untuk menggunakan dan berinteraksi dengan fungsionalitasnya.

2. Since you have completed the tutorial by now and have tried to test your notification system by spawning multiple instances of Receiver, explain how Observer pattern eases you to plug in more subscribers. How about spawning more than 1 instance of Main app, will it still be easy enough to add to the system?

Observer pattern memberikan cara yang fleksibel dan terpisah untuk menangani komunikasi antara komponen-komponen dalam sebuah sistem. Dengan menerapkan pola ini, menambahkan lebih banyak subscriber menjadi lebih mudah karena setiap subscriber dapat mendaftar secara independen dengan penerbit untuk menerima pembaruan. Decoupling ini memungkinkan penambahan subscriber baru ke dalam sistem tanpa memodifikasi kode yang sudah ada atau memengaruhi subscriber lainnya.

Ketika melakukan multiple instansiasi dari aplikasi utama, menambahkan subscriber baru masih dapat dilakukan dengan relatif mudah tergantung dari bagaimana sistem tersebut dirancang. Jika setiap instansi dari aplikasi utama beroperasi secara independen dan memiliki set subscriber sendiri, maka menambahkan subscriber baru ke setiap instansi dapat dilakukan dengan mudah. Setiap instansi dari aplikasi utama dapat mengelola set subscriber sendiri, dan penambahan subscriber baru ke setiap instansi mengikuti proses yang sama seperti menambahkannya ke instansi tunggal.

Namun, jika beberapa instansi dari aplikasi utama perlu berbagi subscriber atau berkomunikasi satu sama lain, maka menambahkan subscriber baru mungkin memerlukan pertimbangan tambahan. Dalam skenario ini, mungkin perlu mengimplementasikan mekanisme untuk berkoordinasi antara instansi, seperti registri subscriber yang dibagikan atau sistem pesan terpusat. Meskipun hal ini mungkin menambah beberapa kompleksitas,Observer Pattern masih dapat memberikan solusi yang fleksibel dan skalabel untuk mengelola komunikasi antara beberapa instansi dari aplikasi utama dan subscriber mereka.

3. Have you tried to make your own Tests, or enhance documentation on your Postman collection? If you have tried those features, tell us whether it is useful for your work (it can be your tutorial work or your Group Project)

Membuat pengujian dan meningkatkan dokumentasi pada koleksi Postman adalah langkah penting dalam pengembangan API. Dengan menambahkan pengujian ke dalam koleksi dapat secara otomatis memverifikasi fungsionalitas API, menghemat waktu dan upaya yang diperlukan untuk pengujian berulang. Sementara itu, peningkatan dokumentasi dengan deskripsi yang jelas dan contoh penggunaan membantu mempercepat proses onboarding pengguna baru dan memfasilitasi kolaborasi tim dalam pengembangan API. Dengan demikian, langkah-langkah ini membantu meningkatkan kualitas dan keterbacaan API, serta memudahkan penggunaan dan adopsi yang sukses.







