# Tutorial 9 - gRPC
AdvPro B - Marvel Martin Everthard - 2206081345

## Reflection
**_1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?_**
- **Unary RPC** merupakan protol komunikasi yang memungkinkan _client_ mengirimkan **_single request_** ke _server_ dan mendapat _**single response**_. Metode ini cocok digunakan untuk **simple request-response scenario**, seperti mengambil suatu data tertentu dari _database_ atau autentikasi _user_.
- **Server Streaming RPC** merupakan protokol komunikasi yang memungkinkan _client_ mengirimkan _request_ ke server dan server **merespons** dengan **aliran pesan yang berurutan**. Metode ini cocok digunakan untuk skenario di mana _server_ perlu **mengembalikan data dalam jumlah besar** atau **mengembalikan serangkaian _update_ berkelanjutan** kepada _client_, seperti update harga _stock market_.
- **Bi-directional RPC** merupakan pola komunikasi di mana _client_ dan _server_ dapat saling **mengirim aliran pesan** secara kontinu. Moetode ini cocok untuk skenario di mana _client_ dan _server_ perlu **mengirim** dan **menerima data** secara **real-time**, seperti aplikasi _chat_. 

_**2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?**_
- **Authentication**: Implementasi mekanisme seperti JWT atau OAuth untuk memverifikasi identitas _client_.
- **Authorization**: Menetapkan aturan kontrol akses untuk memastikan klien hanya dapat mengakses _resource_ yang diizinkan dengan implementasi Role-Based Access Control (RBAC) atau Attribute-Based Access Control (ABAC).
- **Data Encryption**: Mengenkripsi data yang ditransimisi antara _client_ dan _server_ menggunakan TLS/SSL.

__**3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?**__
- **Synchronization**: _Client_ dan _server_ dapat mengirim dan menerima pesan secara konkuren sehingga penanganan sinkronisasi _stream_ yang kurang tepat  dapat menyebabkan _data race_ seperti pesan yang terkirim tidak sesuai urutan.
- **Handling error**: Error dapat terjadi kapan saja selama proses komunikasi, seperti gangguan internet, server down, atau pelanggaran protokol. Menangani error-error tersebut dengan baik sambil menjaga berjalannya sesi obrolan bisa menjadi tantangan.
- **Backpressure**: _Backpressure_ dapat terjadi ketika salah satu dari _client_ atau _server_ mengirim _message_ lebih cepat dibanding yang lainnya dapat memproses _message_ tersebut. Hal ini dapat menyebabkan _buffer_ _overflow_, kehabisan memori, atau kerusakan sistem.

__**4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?**__
**Advantages**:
- `ReceiverStream` memungkinkan penanganan respons yang efisien dalam layanan Rust gRPC tanpa memblokir eksekusi thread yang memungkinkan asinkronus streaming.
- `ReceiverStream` terintegrasi dengan komponen lainnya dari Tokio, seperti runtime dan manajemen jaringan sehingga membuatnya lebih mudah untuk memelihara aplikasi Rust yang asinkronus.
- `ReceiverStream` memberikan fleksibilitas dalam manipulasi aliran data sehingga memungkinkan berbagai skenario streaming yang berbeda.
**Disadvantages**:
- Penggunaan `ReceiverStream` dapat menyebabkan _overhead_ seperti alokasi memori dan konteks _switching_ yang dapat mempengaruhi kinerja aplikasi jika tidak dikelola dengan baik.
- `ReceiverStream` mungkin tidak _compatible_ dengan _platform_ lain di luar Rust.
- `ReceiverStream` kurang optimal untuk digunakan pada skenario _streaming_ yang melibatkan manipulasi data yang kompleks.

__**5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?**__
- **Separation of concern**: Membagi kode menjadi bagian-bagian yang terpisah, misalnya, modul-modul yang mewakili fungsionalitas tertentu.
- **Menggunakan _traits_**: _Traits_ memungkinkan implementasi yang fleksibel dan penggunaan ulang kode.
- **Dependency Injection**: Hindari ketergantungan langsung pada implementasi spesifik dengan menerapkan _dependency injection_ yang memungkinkan komponen-komponen untuk berinteraksi melalui abstraksi.

__**6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?**__


__**7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?**__


__**8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?**__


__**9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?**__


__**10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?**__
