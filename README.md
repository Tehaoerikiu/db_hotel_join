# JOIN (Inner, Left, Right)
## Penjelasan tiap join
### Inner Join 
**Inner Join** adalah salah satu perintah dalam SQL yang digunakan untuk menggabungkan data dari dua atau lebih tabel yang memiliki hubungan berdasarkan kolom tertentu. Perintah ini bekerja dengan cara mencocokkan nilai pada kolom yang memiliki relasi antara tabel pertama dan tabel kedua. Inner Join hanya akan menampilkan data yang memiliki kecocokan (match) pada kedua tabel tersebut. Jika terdapat baris data pada salah satu tabel yang tidak memiliki pasangan yang sesuai pada tabel lainnya, maka data tersebut tidak akan ditampilkan dalam hasil query.

### Left Join
**Left Join** adalah salah satu perintah dalam SQL yang digunakan untuk menggabungkan data dari dua tabel berdasarkan kolom yang saling berhubungan, namun dengan cara yang berbeda dari Inner Join. Pada Left Join, semua data dari tabel sebelah kiri (left table) akan tetap ditampilkan, meskipun tidak memiliki pasangan data yang sesuai pada tabel sebelah kanan (right table). Jika terdapat baris pada tabel kiri yang tidak memiliki kecocokan pada tabel kanan, maka kolom dari tabel kanan akan bernilai NULL pada hasil query.

### Right Join
**Right Join** adalah perintah dalam SQL yang digunakan untuk menggabungkan dua tabel, dengan menampilkan semua data dari tabel sebelah kanan (right table) dan data yang sesuai dari tabel sebelah kiri.

### **Sebagai contoh, kita akan menggunakan kasus hotel dengan 3 tabel (Reservasi, Pelanggan, Kamar)** ### 

## Setup untuk inner, left, right join
### Buat Database dan gunakan Databasenya
```
MariaDB [(none)]> create database db_hotel;
Query OK, 1 row affected (0.001 sec)

MariaDB [(none)]> use db_hotel;
Database changed
```
### buat tabel pelanggan, kamar, dan reservasi
```
create table pelanggan(
    -> id_pelanggan int primary key,
    -> nama varchar(100),
    -> no_telp varchar(15));
Query OK, 0 rows affected (0.027 sec)

create table kamar(
    -> id_kamar int primary key auto_increment,
    -> no_kamar int,
    -> tipe varchar(50),
    -> harga int);
Query OK, 0 rows affected (0.020 sec)

create table reservasi(
    -> id_reservasi int primary key,
    -> id_pelanggan int references pelanggan(id_pelanggan),
    -> id_kamar int references kamar(id_kamar),
    -> tanggal_checkin date,
    -> tanggal_checkout date,
    -> total int);
Query OK, 0 rows affected (0.019 sec)
```
**=> Detail dari tiap tabel <=**
```
=> pelanggan
MariaDB [db_hotel]> desc pelanggan;
+--------------+--------------+------+-----+---------+-------+
| Field        | Type         | Null | Key | Default | Extra |
+--------------+--------------+------+-----+---------+-------+
| id_pelanggan | int(11)      | NO   | PRI | NULL    |       |
| nama         | varchar(100) | YES  |     | NULL    |       |
| no_telp      | varchar(15)  | YES  |     | NULL    |       |
+--------------+--------------+------+-----+---------+-------+
3 rows in set (0.029 sec)

=> kamar
MariaDB [db_hotel]> desc kamar;
+----------+-------------+------+-----+---------+----------------+
| Field    | Type        | Null | Key | Default | Extra          |
+----------+-------------+------+-----+---------+----------------+
| id_kamar | int(11)     | NO   | PRI | NULL    | auto_increment |
| no_kamar | int(11)     | YES  |     | NULL    |                |
| tipe     | varchar(50) | YES  |     | NULL    |                |
| harga    | int(11)     | YES  |     | NULL    |                |
+----------+-------------+------+-----+---------+----------------+
4 rows in set (0.037 sec)

=> reservasi
MariaDB [db_hotel]> desc reservasi;
+------------------+---------+------+-----+---------+-------+
| Field            | Type    | Null | Key | Default | Extra |
+------------------+---------+------+-----+---------+-------+
| id_reservasi     | int(11) | NO   | PRI | NULL    |       |
| id_pelanggan     | int(11) | YES  |     | NULL    |       |
| id_kamar         | int(11) | YES  |     | NULL    |       |
| tanggal_checkin  | date    | YES  |     | NULL    |       |
| tanggal_checkout | date    | YES  |     | NULL    |       |
| total            | int(11) | YES  |     | NULL    |       |
+------------------+---------+------+-----+---------+-------+
6 rows in set (0.013 sec)
```
### isi data pelanggan dan kamar
```
insert into pelanggan values
    -> (1, 'Budi', '08129988'),
    -> (2, 'Thoriq', '08129721'),
    -> (3, 'Andi', '08129123'),
    -> (4, 'Rizky', '08962280'),
    -> (5, 'Dahab', '08965233');
Query OK, 5 rows affected (0.014 sec)
Records: 5  Duplicates: 0  Warnings: 0

insert into kamar values
    -> (1, 101, 'Standard', 300000),
    -> (2, 102, 'Silver', 400000),
    -> (3, 103, 'Deluxe', 550000),
    -> (4, 104, 'Premium', 700000);
Query OK, 4 rows affected (0.027 sec)
Records: 4  Duplicates: 0  Warnings: 0
```
### isi tabel reservasi dengan bebas tapi benar (untuk id pelanggan, kamar, harus sesuai dengan tabel yang ada di pelanggan / kamar)
```
insert into reservasi values
    -> (1, 1, 2, '2026-03-04', '2026-03-05', 400000),
    -> (2, 2, 4, '2026-03-04', '2026-03-06', 1400000),
    -> (3, 5, 2, '2026-05-07', '2026-05-26', 800000);
Query OK, 3 rows affected (0.002 sec)
Records: 3  Duplicates: 0  Warnings: 0
```
## Perintah Inner Join
```
MariaDB [db_hotel]> select pelanggan.nama, kamar.no_kamar, kamar.tipe, reservasi.tanggal_checkin, reservasi.tanggal_checkout, reservasi.total from reservasi inner join pelanggan on reservasi.id_pelanggan = pelanggan.id_pelanggan inner join kamar on reservasi.id_kamar = kamar.id_kamar;
+--------+----------+---------+-----------------+------------------+---------+
| nama   | no_kamar | tipe    | tanggal_checkin | tanggal_checkout | total   |
+--------+----------+---------+-----------------+------------------+---------+
| Budi   |      102 | Silver  | 2026-03-04      | 2026-03-05       |  400000 |
| Dahab  |      102 | Silver  | 2026-05-07      | 2026-05-26       |  800000 |
| Thoriq |      104 | Premium | 2026-03-04      | 2026-03-06       | 1400000 |
+--------+----------+---------+-----------------+------------------+---------+
3 rows in set (0.001 sec)
```
Perintah INNER JOIN pada query tersebut digunakan untuk menggabungkan data dari tabel reservasi, pelanggan, dan kamar. Tabel reservasi dijadikan sebagai tabel utama, kemudian dihubungkan dengan tabel pelanggan melalui id_pelanggan dan dengan tabel kamar melalui id_kamar. Query tersebut menampilkan informasi nama pelanggan, nomor kamar, tipe kamar, tanggal check-in, tanggal check-out, dan total pembayaran. Karena menggunakan INNER JOIN, hanya data yang memiliki hubungan pada ketiga tabel yang akan ditampilkan.
## Perintah Left Join
```
MariaDB [db_hotel]> select pelanggan.nama, kamar.no_kamar, kamar.tipe, reservasi.tanggal_checkin, reservasi.tanggal_checkout, reservasi.total from reservasi left join pelanggan on reservasi.id_pelanggan = pelanggan.id_pelanggan right join kamar on reservasi.id_kamar = kamar.id_kamar;
+--------+----------+----------+-----------------+------------------+---------+
| nama   | no_kamar | tipe     | tanggal_checkin | tanggal_checkout | total   |
+--------+----------+----------+-----------------+------------------+---------+
| NULL   |      101 | Standard | NULL            | NULL             |    NULL |
| Budi   |      102 | Silver   | 2026-03-04      | 2026-03-05       |  400000 |
| Dahab  |      102 | Silver   | 2026-05-07      | 2026-05-26       |  800000 |
| NULL   |      103 | Deluxe   | NULL            | NULL             |    NULL |
| Thoriq |      104 | Premium  | 2026-03-04      | 2026-03-06       | 1400000 |
| NULL   |      105 | VIP      | NULL            | NULL             |    NULL |
+--------+----------+----------+-----------------+------------------+---------+
6 rows in set (0.001 sec)
```

