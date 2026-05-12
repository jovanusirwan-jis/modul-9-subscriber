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

