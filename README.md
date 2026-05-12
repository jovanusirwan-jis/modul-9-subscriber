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

![grafik pakai thread berkali kali ](<gambarpakai thread.jpeg>)

