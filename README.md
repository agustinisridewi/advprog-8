### AdvProg Tutorial 8
Agus Tini Sridewi / 2306276004 / ADPRO A

<strong> 1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable? </strong>

Perbedaan utama antara unary, server streaming, dan bidirectional streaming dalam RPC terletak pada arah dan jumlah data yang ditransmisikan. 
- Unary RPC adalah panggilan satu-ke-satu: klien mengirim satu permintaan dan server memberikan satu respons, cocok untuk operasi sederhana seperti autentikasi atau pengambilan data tunggal. 
- Server streaming memungkinkan klien mengirim satu permintaan dan menerima aliran banyak respons—ideal untuk skenario seperti memantau log atau pembaruan status secara real-time. 
- Bidirectional streaming memungkinkan klien dan server saling bertukar aliran data secara bersamaan dan independen, sangat sesuai untuk aplikasi interaktif seperti chat atau video streaming.



<strong> 2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption? </strong>

Saat mengimplementasikan layanan gRPC di Rust, ada beberapa aspek keamanan yang perlu diperhatikan:
- Autentikasi, memastikan hanya klien yang sah yang dapat mengakses layanan, biasanya dengan menggunakan token seperti JWT atau TLS mutual authentication. 
- Authorization, diperlukan untuk membatasi akses pengguna ke endpoint tertentu sesuai hak aksesnya. 
- Enkripsi data menggunakan TLS, penting untuk melindungi data yang ditransmisikan agar tidak bisa disadap atau dimodifikasi pihak ketiga. 
- Dalam konteks Rust, kombinasi library seperti tonic dan rustls bisa digunakan untuk menyediakan lapisan keamanan ini dengan kinerja yang efisien.

<strong> 3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?</strong>

Tantangan utama dalam menangani bidirectional streaming gRPC di Rust terutama berkaitan dengan manajemen state asynchronous yang kompleks. Karena Rust sangat ketat dalam hal kepemilikan data dan concurrency, pengelolaan sink dan stream secara bersamaan dalam satu koneksi bisa memicu borrow checker errors atau deadlock jika tidak hati-hati. Dalam aplikasi chat misalnya, sink harus tetap terbuka untuk mengirim pesan sambil secara paralel mendengarkan stream masuk, sehingga membutuhkan task atau thread yang berjalan simultan (biasanya menggunakan tokio::spawn).

<strong> 4. What are the advantages and disadvantages of using the ```rust tokio_stream::wrappers::ReceiverStream``` for streaming responses in Rust gRPC services? </strong> 

Kelebihan: ReceiverStream mudah diintegrasikan dengan channel mpsc, memungkinkan pengiriman item secara asinkron, ideal untuk aplikasi seperti long-polling atau notifikasi real-time.

Kekurangan: Tidak semua jenis stream cocok dengan mpsc, dan pengelolaan kapasitas buffer serta error handling bisa menambah kompleksitas, berpotensi menyebabkan deadlock atau race condition jika tidak ditangani dengan baik.

<strong> 5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time? </strong>

Untuk menjaga modularitas dan memungkinkan reuse struktur kode sebaiknya dipisah ke dalam modul-modul yang jelas. Misalnya, definisi protokol (gRPC generated code) ditempatkan di satu modul tersendiri (mod proto), logika bisnis di services, handler gRPC di handlers, dan validasi input di validators. 

Reusability dicapai dengan mendefinisikan logika utama sebagai trait, lalu diimplementasikan di struct yang bisa disuntikkan (dengan Arc), sehingga modul seperti testing, CLI, atau layanan lain bisa menggunakan ulang service yang sama tanpa duplikasi.


<strong> 6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?</strong>

Untuk menangani logika pembayaran yang lebih kompleks dalam MyPaymentService, dibutuhkan beberapa langkah tambahan seperti validasi input agar data yang masuk sesuai format dan aturan serta pencatatan transaksi ke database agar histori dapat ditelusuri. Selain itu, perlu juga penanganan error jika terjadi kegagalan transaksi, serta membuat response yang menyertakan informasi detail seperti status pembayaran, ID transaksi, atau pesan kesalahan jika ada.

<strong>7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms? </strong>

Adopsi gRPC mengarah pada desain sistem yang lebih ketat dan efisien karena penggunaan Protocol Buffers yang kuat secara tipe dan lebih cepat diproses dibandingkan JSON. Ini memudahkan pengembangan antar layanan internal dalam ekosistem yang homogen (misalnya semua menggunakan Rust, Go, atau Java). Namun, interoperabilitas bisa jadi tantangan jika layanan lain tidak mendukung gRPC secara native.

<strong> 8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?</strong>

Kelebihan: Kemampuan multiplexing yang memungkinkan banyak permintaan berjalan paralel, serta kompresi header (HPACK) yang mengurangi overhead komunikasi. HTTP/2 juga mendukung prioritas request dan pipelining yang membuatnya cocok untuk arsitektur microservices dan aplikasi real-time. 

Kekurangan: Debugging lebih rumit karena format biner, dan tidak semua tool atau proxy HTTP lama mendukung HTTP/2 secara penuh.

<strong> 9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness? </strong>

REST API mengikuti pola request-response klasik, di mana klien mengirim permintaan lalu menunggu respons. Sebaliknya, gRPC memungkinkan bidirectional streaming, di mana klien dan server bisa saling mengirim data secara asinkron dalam satu koneksi. Karena itu, responsivitas gRPC lebih tinggi dalam kasus komunikasi terus-menerus.

<strong> 10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads? </strong>

gRPC menggunakan Protocol Buffers yang memaksa pengembang untuk mendefinisikan skema yang eksplisit dan dikompilasi ke kode, sehingga menjamin konsistensi data dan validasi tipe sejak awal. Ini mempercepat parsing dan mengurangi error saat integrasi. Sebaliknya, JSON di REST API lebih fleksibel dan mudah dibaca manusia, tapi berisiko menghasilkan error saat struktur data berubah (misalnya field hilang atau tipe salah). 
Perubahan skema di gRPC juga lebih aman karena backward compatibility bisa diatur secara eksplisit, sedangkan REST membutuhkan dokumentasi dan validasi manual.

