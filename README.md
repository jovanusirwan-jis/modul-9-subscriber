a. Apa itu AMQP?

AMQP adalah singkatan dari Advanced Message Queuing Protocol. AMQP merupakan protokol komunikasi yang digunakan untuk mengirim pesan antar aplikasi atau antar service.

AMQP sering digunakan pada software seperti RabbitMQ untuk:

komunikasi asynchronous, message queue (antrian pesan), sistem terdistribusi, microservices.

Dengan AMQP, sebuah aplikasi bisa mengirim pesan ke queue, lalu aplikasi lain bisa mengambil dan memproses pesan tersebut nanti.

Contoh:

Service A mengirim data pesanan. Data masuk ke queue. Service B mengambil dan memproses data tersebut.

Hal ini membuat sistem lebih stabil dan scalable.

b. Arti dari guest:guest@localhost:5672

Itu biasanya adalah bagian dari connection string untuk terhubung ke server AMQP seperti RabbitMQ.

Penjelasannya:

guest : guest @ localhost : 5672

guest pertama
Merupakan username untuk login.

guest kedua
Merupakan password dari user tersebut.

Jadi:

username = guest password = guest

localhost
localhost berarti server berjalan di komputer yang sama dengan aplikasi yang digunakan.

Biasanya localhost mengarah ke:

127.0.0.1

yaitu alamat komputer sendiri.

5672
Angka 5672 adalah port yang digunakan oleh AMQP/RabbitMQ untuk komunikasi.

Port bisa dianggap seperti “jalur komunikasi” agar aplikasi dapat terhubung ke server RabbitMQ.

Jadi secara keseluruhan:

guest:guest@localhost:5672

berarti: Menghubungkan aplikasi ke server RabbitMQ di komputer sendiri (localhost) melalui port 5672 menggunakan username guest dan password guest.

Refleksi pakai thread dan ga pakai thread  : 

Perbedaan utama dari kedua metrik di dashboard RabbitMQ tersebut terletak pada beban pesan (message load) yang dikirim ke antrean. Saat program dijalankan berkali-kali (gambar pertama/kiri), sistem menerima dan menampung lebih banyak pesan dalam satu waktu dibandingkan saat hanya dijalankan 3 kali (gambar kedua/kanan).

Berikut adalah rincian perbedaan yang terlihat pada grafik:

Puncak Antrean Pesan (Queued Messages):

Gambar Pertama (Kiri): Grafik Queued messages menunjukkan lonjakan yang lebih tinggi, memuncak di sekitar 16 pesan yang mengantre.

Gambar Kedua (Kanan): Grafik menunjukkan puncak antrean yang lebih rendah, yaitu hanya mencapai sekitar 11 pesan.

Kecepatan Pengiriman Pesan (Message Rates - Publish):

Gambar Pertama (Kiri): Garis merah pada grafik Message rates (menandakan Publish) mencapai puncak di angka 3.0 pesan per detik (3.0/s). Ini berarti banyak thread yang berjalan bersamaan mengirimkan pesan dengan laju yang lebih cepat.

Gambar Kedua (Kanan): Garis merah memuncak lebih rendah di angka 2.0 pesan per detik (2.0/s) karena jumlah pengiriman pesan (thread) lebih sedikit.

Pola Pemrosesan oleh Consumer:

Pada kedua gambar, garis ungu (Consumer ack) menunjukkan kecepatan consumer dalam memproses pesan, yang puncaknya konstan di kisaran 1.0/s.

Bedanya, pada gambar pertama, garis ungu (durasi pemrosesan) akan membentang sedikit lebih panjang atau membutuhkan waktu lebih lama untuk mengosongkan antrean sampai kembali ke angka 0, karena jumlah pesan awal yang masuk lebih banyak.

Singkatnya, menjalankan thread lebih banyak akan menghasilkan lonjakan (spike) yang lebih tinggi pada grafik Queued messages dan Publish rates karena RabbitMQ harus menangani volume pesan masuk yang lebih besar dalam waktu yang bersamaan.
![grafik pakai thread berkali kali ](<gambarpakai thread.jpeg>)
![grafik pakai thread 3 kali](<Screenshot 2026-05-12 191640-2.png>)
![cargo run 3 kali](<Screenshot 2026-05-12 183020-1.png>)


Refleksi untuk bonus :
Refleksi: Deployment Cloud, Konfigurasi Port, dan Evaluasi Commit Message

Pada eksperimen kali ini, saya melakukan transisi dari lingkungan pengembangan lokal (local machine) ke lingkungan cloud. Proses ini memberikan beberapa insight baru, terutama terkait perbedaan arsitektur jaringan, keamanan, dan bagaimana kita mendokumentasikan proses pengembangan melalui commit message.

1. Tantangan di Lingkungan Cloud dan Konfigurasi Firewall
Ketika menjalankan aplikasi (seperti publisher, subscriber, dan message broker RabbitMQ) di mesin lokal, semua proses berjalan di dalam localhost sehingga tidak ada restriksi jaringan yang ketat. Namun, saat berpindah ke cloud, saya belajar bahwa keamanan adalah prioritas (biasanya default-deny).

Untuk membuat aplikasi bisa saling berkomunikasi atau diakses dari luar, saya harus secara eksplisit membuka port untuk koneksi eksternal pada konfigurasi firewall (misalnya Security Groups atau Inbound Rules di provider cloud). Contohnya, membuka port khusus untuk protokol AMQP agar publisher/subscriber bisa terhubung ke RabbitMQ, atau port UI manajemen agar dashboard bisa diakses melalui browser. Hal ini menyadarkan saya betapa pentingnya pemahaman mengenai networking dan access control saat melakukan deployment ke production.

2. Re-evaluasi Commit Message: "Make it works" dan "Simulating slow subscriber"
Instruksi untuk mengulang/meninjau kembali commit message pada bagian "Make it works" dan "Simulating slow subscriber" mengingatkan saya pada pentingnya clean code dan sejarah pengembangan (git history) yang deskriptif.

Pada fase "Make it works": Seringkali saat development, kita cenderung menggunakan commit message asal-asalan asalkan kode berhasil berjalan (misal: "fix bug" atau "make it work"). Melalui refleksi ini, saya menyadari bahwa commit message harus menjelaskan apa yang diubah dan mengapa perubahan itu membuat sistem berfungsi, misalnya: "feat: configure AMQP connection URI to point to cloud instance" atau "fix: update environment variables for remote message broker".

Pada fase "Simulating slow subscriber": Commit message di sini harus merepresentasikan intent simulasi. Saat mengimplementasikan slow subscriber, saya merekayasa consumer agar memproses pesan lebih lambat dari laju publisher (misalnya dengan menyisipkan thread::sleep). Pesan commit yang baik harus menceritakan simulasi ini, contohnya: "test: introduce processing delay in subscriber thread to simulate bottleneck and observe RabbitMQ message queuing". Ini akan memudahkan developer lain (atau saya di masa depan) mengerti bahwa kode tersebut diubah untuk tujuan eksperimen beban antrean, bukan karena performa sistem yang buruk.

Kesimpulan
Secara keseluruhan, eksperimen di cloud ini tidak hanya menguji kemampuan saya menulis kode yang berfungsi, tetapi juga menguji pemahaman saya tentang infrastruktur jaringan (firewall/ports) dan kedisiplinan dalam menulis dokumentasi iterasi codebase melalui commit message yang bermakna. Hal ini sangat krusial untuk simulasi sistem terdistribusi yang nyata. 
