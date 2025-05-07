> 1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

Unary RPC merupakan pola komunikasi paling sederhana di gRPC, di mana klien mengirim satu permintaan dan server merespons dengan satu jawaban. Ini cocok untuk operasi yang sifatnya atomic, seperti pengambilan data berdasarkan ID atau permintaan login. Server streaming RPC memungkinkan server mengirim serangkaian respons secara bertahap setelah menerima satu permintaan dari klien, yang ideal untuk kasus seperti pengiriman data besar secara bertahap atau pembaruan progresif. Sementara itu, bi-directional streaming RPC memungkinkan klien dan server saling bertukar pesan secara asinkron dalam waktu bersamaan, sehingga sangat sesuai untuk aplikasi real-time seperti sistem chat, kolaborasi dokumen, atau live location sharing.

> 2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

Dalam implementasi gRPC dengan Rust, aspek keamanan sangat penting dan mencakup tiga hal utama: enkripsi, autentikasi, dan otorisasi. Enkripsi biasanya dilakukan menggunakan TLS untuk menjamin kerahasiaan data yang dikirim antar entitas. Untuk autentikasi, umum digunakan pendekatan token berbasis JWT atau sertifikat X.509, tergantung kebutuhan dan sensitivitas data. Di sisi otorisasi, pendekatan berbasis peran (Role-Based Access Control) direkomendasikan untuk membatasi akses pengguna hanya pada metode atau data yang diizinkan. Selain itu, validasi input dan pembatasan ukuran pesan juga perlu diterapkan guna mencegah serangan seperti buffer overflow atau denial-of-service.

> 3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

Tantangan utama dalam implementasi bi-directional streaming gRPC di Rust terletak pada pengelolaan sinkronisasi pesan antara server dan banyak klien secara bersamaan. Karena komunikasi bersifat asinkron, perlu perhatian ekstra dalam mengelola antrian pesan dan menjaga urutan pengiriman. Selain itu, kestabilan koneksi menjadi isu penting—klien yang terputus secara tiba-tiba harus ditangani dengan logika fallback atau reconnect. Dari sisi sistem, pengelolaan resource seperti memori dan thread perlu dirancang dengan efisien agar performa tetap optimal, apalagi jika banyak stream aktif berjalan secara paralel.

> 4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

Penggunaan tokio_stream::wrappers::ReceiverStream memberikan integrasi yang baik dengan ekosistem async Rust dan mempermudah pembuatan stream respons berbasis channel. Keunggulannya adalah alur kerja yang natural dengan async/await, serta kemampuan membangun aliran data dari task-task lain dalam sistem. Namun, kerugian utamanya adalah peningkatan kompleksitas debugging saat terjadi masalah pada alur asinkron, terutama dalam kasus deadlock atau dropped stream. Di sisi lain, overhead performa dapat muncul jika tidak ada pengelolaan yang efisien pada channel dan buffer-nya.

> 5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

Struktur kode gRPC di Rust sebaiknya dipisah ke dalam modul yang jelas: definisi Protobuf (biasanya di folder proto/), implementasi layanan (services/), serta logika bisnis (logic/ atau domain/). Pendekatan berbasis trait untuk abstraksi layanan dan dependency injection (misalnya dengan Arc<dyn Trait>) memudahkan pengujian serta penggantian komponen. Modularisasi ini juga mendukung pengembangan jangka panjang karena tiap bagian kode dapat diperluas atau diuji secara independen tanpa memengaruhi keseluruhan sistem.

> 6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

Untuk menangani logika pemrosesan pembayaran yang lebih kompleks, perlu dilakukan validasi data transaksi (misalnya nominal dan tujuan), verifikasi dengan gateway pembayaran eksternal, serta pencatatan status transaksi secara real-time. Penanganan kesalahan harus dirancang secara menyeluruh agar dapat membedakan antara error sistem, error pengguna, dan error dari pihak ketiga. Di sisi keamanan dan audit, penting untuk mencatat semua aktivitas dalam log terenkripsi dan menyediakan mekanisme rollback jika terjadi kegagalan di tengah proses transaksi.

> 7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

Adopsi gRPC membawa dampak positif bagi arsitektur sistem terdistribusi karena mendukung interoperabilitas antar layanan berbahasa berbeda melalui Protocol Buffers. Protokol ini menyediakan komunikasi yang lebih cepat dan efisien karena menggunakan HTTP/2, yang mendukung multiplexing dan kompresi header. Namun, sistem yang sebelumnya berbasis REST mungkin memerlukan penyesuaian besar, seperti transisi dari JSON ke Protobuf dan adaptasi pipeline CI/CD untuk mendukung kompilasi file .proto.

> 8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

HTTP/2 menawarkan banyak keunggulan seperti multiplexing (menghindari head-of-line blocking), kompresi header, dan latensi rendah berkat koneksi yang tetap terbuka. Ini menjadikannya sangat ideal untuk skenario real-time dan high throughput. Namun, kompleksitas implementasi lebih tinggi dibandingkan HTTP/1.1, dan tidak semua alat pengembang atau jaringan mendukung HTTP/2 secara penuh. Selain itu, debugging HTTP/2 bisa lebih menantang dibandingkan HTTP/1.1 karena protokolnya lebih abstrak.

> 9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

REST API bekerja dengan model request-response klasik, yang berarti klien harus menunggu server menyelesaikan proses sebelum mendapatkan respons. Ini membatasi efisiensi dan responsivitas, terutama dalam aplikasi real-time. Sebaliknya, gRPC mendukung streaming dua arah, memungkinkan pertukaran data secara terus-menerus antara klien dan server tanpa harus menutup koneksi. Hasilnya, aplikasi seperti chat, monitoring, atau game online bisa berjalan lebih mulus dan cepat dengan gRPC dibandingkan REST.

> 10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

Pendekatan berbasis skema seperti Protobuf di gRPC memberikan struktur data yang ketat dan efisien, serta memungkinkan validasi dan kompresi data yang lebih baik. Hal ini mempercepat serialisasi/deserialisasi dan mengurangi ukuran payload. Namun, kekurangannya adalah kurangnya fleksibilitas: perubahan pada struktur data membutuhkan re-generasi kode dan sinkronisasi antar layanan. Sebaliknya, JSON yang digunakan pada REST API lebih fleksibel dan mudah dimodifikasi tanpa proses build tambahan, meski lebih boros ukuran dan rentan terhadap kesalahan tipe data.
