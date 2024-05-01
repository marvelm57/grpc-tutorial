# Tutorial 9 - gRPC
AdvPro B - Marvel Martin Everthard - 2206081345

## Reflection
**1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?**
- **Unary RPC** merupakan protol komunikasi yang memungkinkan _client_ mengirimkan **_single request_** ke _server_ dan mendapat _**single response**_. Metode ini cocok digunakan untuk **simple request-response scenario**, seperti mengambil suatu data tertentu dari _database_ atau autentikasi _user_.
- **Server Streaming RPC** merupakan protokol komunikasi yang memungkinkan _client_ mengirimkan _request_ ke server dan server **merespons** dengan **aliran pesan yang berurutan**. Metode ini cocok digunakan untuk skenario di mana _server_ perlu **mengembalikan data dalam jumlah besar** atau **mengembalikan serangkaian _update_ berkelanjutan** kepada _client_, seperti update harga _stock market_.
- **Bi-directional RPC** merupakan pola komunikasi di mana _client_ dan _server_ dapat saling **mengirim aliran pesan** secara kontinu. Moetode ini cocok untuk skenario di mana _client_ dan _server_ perlu **mengirim** dan **menerima data** secara **real-time**, seperti aplikasi _chat_. 

**2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?**
- **Authentication**: Implementasi mekanisme seperti JWT atau OAuth untuk memverifikasi identitas _client_.
- **Authorization**: Menetapkan aturan kontrol akses untuk memastikan klien hanya dapat mengakses _resource_ yang diizinkan dengan implementasi Role-Based Access Control (RBAC) atau Attribute-Based Access Control (ABAC).
- **Data Encryption**: Mengenkripsi data yang ditransimisi antara _client_ dan _server_ menggunakan TLS/SSL.

**3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?**
- **Synchronization**: _Client_ dan _server_ dapat mengirim dan menerima pesan secara konkuren sehingga penanganan sinkronisasi _stream_ yang kurang tepat  dapat menyebabkan _data race_ seperti pesan yang terkirim tidak sesuai urutan.
- **Handling error**: Error dapat terjadi kapan saja selama proses komunikasi, seperti gangguan internet, server down, atau pelanggaran protokol. Menangani error-error tersebut dengan baik sambil menjaga berjalannya sesi obrolan bisa menjadi tantangan.
- **Backpressure**: _Backpressure_ dapat terjadi ketika salah satu dari _client_ atau _server_ mengirim _message_ lebih cepat dibanding yang lainnya dapat memproses _message_ tersebut. Hal ini dapat menyebabkan _buffer_ _overflow_, kehabisan memori, atau kerusakan sistem.

**4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?**

**Advantages**:
- `ReceiverStream` memungkinkan penanganan respons yang efisien dalam layanan Rust gRPC tanpa memblokir eksekusi thread sehingga memungkinkan asinkronus streaming.
- `ReceiverStream` terintegrasi dengan komponen lainnya dari Tokio, seperti runtime dan manajemen jaringan sehingga membuatnya lebih mudah untuk memelihara aplikasi Rust yang asinkronus.
- `ReceiverStream` memberikan fleksibilitas dalam manipulasi aliran data sehingga memungkinkan berbagai skenario streaming yang berbeda.

**Disadvantages**:
- Penggunaan `ReceiverStream` dapat menyebabkan _overhead_ seperti alokasi memori dan konteks _switching_ yang dapat mempengaruhi kinerja aplikasi jika tidak dikelola dengan baik.
- `ReceiverStream` mungkin tidak _compatible_ dengan _platform_ lain di luar Rust.
- `ReceiverStream` kurang optimal untuk digunakan pada skenario _streaming_ yang melibatkan manipulasi data yang kompleks.

**5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?**
- **Separation of concern**: Membagi kode menjadi bagian-bagian yang terpisah, misalnya, modul-modul yang mewakili fungsionalitas tertentu.
- **Menggunakan _traits_**: _Traits_ memungkinkan implementasi yang fleksibel dan penggunaan ulang kode.
- **Dependency Injection**: Hindari ketergantungan langsung pada implementasi spesifik dengan menerapkan _dependency injection_ yang memungkinkan komponen-komponen untuk berinteraksi melalui abstraksi.

**6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?**
- **Validasi request**: Pastikan `PaymentRequest` yang masuk berisi semua informasi yang diperlukan dan datanya valid.
- **Penanganan error**: Implementasikan penanganan error, seperti penanganan kesalahan validasi.
- **Interaksi dengan database**

**7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?**
Adopsi gRPC sebagai protokol komunikasi dapat memiliki dampak besar pada arsitektur dan desain distribusi sitem karena gRPC menggunakan protokol `HTTP/2` dan serialisasi `Protobuf` yang efisien yang dapat meningkatkan kinerja dan skalabilitas aplikasi. Namun, hal ini juga bisa mempengaruhi interoperabilitas dengan teknologi dan platform lain. Misalnya, gRPC dapat membatasi interoperabilitas dengan teknologi yang tidak mendukung HTTP/2 atau Protobuf. Untuk mengatasi hal ini, perlu dipertimbangkan untuk menyediakan gateway atau proxy yang memungkinkan aplikasi gRPC berkomunikasi dengan teknologi lain melalui protokol yang dapat diterima secara umum, seperti REST atau JSON. 


**8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?**

**Advantages**:
- **Multiplexing**: HTTP/2 mendukung multiplexing yang memungkinkan beberapa _request_ dan _response_ dikirim dan diterima secara bersamaan melalui satu koneksi TCP tunggal.
- **Kompresi header**: HTTP/2 menggunakan kompresi header sehingga mengurangi _overhead_ dari pengiriman informasi header yang redundan dengan setiap permintaan.
- **Push server**: HTTP/2 memungkinkan server untuk secara proaktif mendorong _resource_ ke klien tanpa menunggu request.

**Disadvantages**:
- **Kompleksitas**: Mengimplementasikan HTTP/2 bisa lebih kompleks daripada HTTP/1.1 yang dapat meningkatkan upaya pengembangan dan biaya pemeliharaan aplikasi.
- **Kompatibilitas**: Meskipun HTTP/2 didukung secara luas oleh browser web dan server modern, sistem-sistem _legacy_ mungkin tidak sepenuhnya mendukung protokol tersebut.
- **Konsumsi resource**: Fitur multiplexing HTTP/2 dapat menyebabkan peningkatan konsumsi sumber daya pada server karena menangani beberapa aliran bersamaan memerlukan pemrosesan tambahan dan overhead memori dibandingkan dengan HTTP/1.1.

**9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?**

Model request-response dari REST API mengikuti pola request-response sederhana di mana klien mengirim _request_ ke server dan server memberikan _response_ dengan data yang diminta sehingga lebih cocok untuk komunikasi yang sederhana dan tidak memerlukan respons instan. Sedangkan, gRPC menggunakan bi-directional streaming yang memungkinkan klien dan server untuk mengirim dan menerima data secara bersamaan dalam satu koneksi. Ini memungkinkan komunikasi real-time yang cepat dan responsif dan cocok untuk aplikasi yang memerlukan interaksi langsung antara klien dan server, seperti _online game_.

**10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?**


Pendekatan berbasis skema yang digunakan oleh gRPC dengan Protocol Buffers memiliki implikasi yang berbeda dibandingkan dengan sifat yang lebih fleksibel dan tanpa skema dari JSON dalam muatan REST API. Dengan Protocol Buffers, skema digunakan untuk mendefinisikan struktur data dengan jelas. Ini berarti bahwa semua pesan yang dikirim dan diterima harus sesuai dengan skema yang telah ditetapkan sebelumnya untuk memastikan konsistensi dan kejelasan dalam komunikasi antara klien dan server. Sedangkan, JSON dalam REST API tidak memerlukan skema yang didefinisikan sehingga memberikan fleksibilitas yang lebih besar dalam mengirim dan menerima data. Namun, ini juga dapat menyebabkan masalah jika tidak ada standar yang ketat, karena klien dan server dapat memiliki interpretasi yang berbeda tentang format data. Oleh karena itu, pendekatan berbasis skema dari gRPC dengan Protocol Buffers menyediakan **kejelasan** dan **konsistensi yang lebih besar** dalam **komunikasi data**, sementara pendekatan yang lebih fleksibel JSON dalam REST API memungkinkan untuk adaptasi yang lebih mudah tetapi dengan risiko kurangnya konsistensi dan interpretasi yang tidak konsisten.
