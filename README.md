# Laporan Praktikum Big Data
Repository ini berisi rangkaian praktikum Big Data yang mencakup penyimpanan terdistribusi (HDFS, MongoDB, Cassandra), pemrosesan data skala besar menggunakan MapReduce dan Apache Spark, pembangunan pipeline ingestion dengan Sqoop, Flume, dan Kafka, serta proses pra-pemrosesan dan feature engineering menggunakan PySpark sebagai dasar pemahaman ekosistem Big Data secara menyeluruh.

<br> 

| Variable           |             Isi            |
| -------------------|----------------------------|
| **Nama**           |     Fadil Aditya Adzima    |
| **NIM**            |          312310617         |
| **Mata Kuliah**    |           Big Data          |
| **Dosen Pengampu** | Agung Nugroho S.Kom., M.Kom.|

<br>

## Praktikum 1
Pastikan perangkat lunak berikut sudah terpasang:

*   **Java 8+**: Diperlukan untuk komponen Hadoop/HDFS.
*   **Git**: Diperlukan untuk mengkloning dataset atau repositori kode.
*   **Docker** (Opsional): Mempermudah proses instalasi dan manajemen dependensi.

### Bagian 1 Praktikum HDFS 
1. **Membuat direktori praktikum di HDFS**
   ```bash
   hdfs dfs -mkdir /praktikum
   ```
   ![Picture for HDFS](assets/assetshdfs/hdfs1.png) <br> <br>

2. **Membuat sebuah file beserta dummy data pada file datasest.csv**
   ```bash
    cat > dataset.csv << EOF
    nim,nama,jurusan,ipk
    12345,Andi,Informatika,3.75
    12346,Budi,Sistem Informasi,3.50
    12347,Citra,Teknik Komputer,3.85
    12348,Doni,Informatika,3.60
    12349,Eka,Sistem Informasi,3.90
    12350,Fani,Teknik Komputer,3.45
    12351,Gita,Informatika,3.70
    12352,Hadi,Sistem Informasi,3.55
    EOF
   ```
   ![Picture for HDFS](assets/assetshdfs/hdfs2.png) <br> <br>

3. **Mengunggah dataset ke HDFS & Verfikasi isi direktori**
   ```bash
   ## Perintah untuk upload dataset ke hdfs
   hdfs dfs -put dataset.csv /praktikum/

   ## Perintah untuk memeriksa isi dari direktori praktikum
   hdfs dfs -ls /praktikum/
   ```
   ![Picture for HDFS](assets/assetshdfs/hdfs3.png) <br> <br>

4. **Membaca isi dataset langsung dari HDFS**
   ```bash
   hdfs dfs -cat /praktikum/dataset.csv
   ```
   ![Picture for HDFS](assets/assetshdfs/hdfs4.png) <br> <br>
   

#### Latihan: coba upload file besar (>100MB) dan periksa apakah file tersebut terpecah menjadi blok-blok kecil di HDFS.
1. **Membuat file dummy 150MB lalu upload file besar tersebut ke dalam direktori praktikum**
   ```bash
   # Buat file dummy 150MB
   dd if=/dev/zero of=bigfile.dat bs=1M count=150
   
   # Upload ke HDFS
   hdfs dfs -put bigfile.dat /praktikum/
   ```
   ![Picture for HDFS](assets/assetshdfs/hdfs5.png) <br> <br>

2. **Periksa detail file telah terpecah menjadi beberapa blok**
   ```bash
   hdfs fsck /praktikum/bigfile.dat -files -blocks -locations
   ```
   ![Picture for HDFS](assets/assetshdfs/hdfs6.png) <br> <br>

**Penjelasan Singkat**
File bigfile.dat berukuran 150MB (157,286,400 bytes) dipecah oleh HDFS menjadi 2 blok dengan ukuran rata-rata sekitar 78.6MB per blok, karena HDFS secara otomatis memecah file besar menjadi blok-blok dengan ukuran default (biasanya 128MB atau lebih kecil tergantung konfigurasi) untuk memudahkan penyimpanan terdistribusi, paralelisme pemrosesan, dan fault tolerance dalam sistem big data.

<br> <br>



### Bagian 2 Praktikum MongoDB
1. **Memilih database yang ingin digunakan**
   ```mongodb
   use praktikum
   ```
   ![Picture for MongoDB](assets/assetsmongodb/mongo1.png) <br>
   Pastikan database otomatis dibuat saat perintah insert pertama dijalankan. <br> <br>

2. **Menambahkan data pada tabel mahasiswa**
   ```mongodb
   db.mahasiswa.insertOne({ nim: "312310617", nama: "Fadil", jurusan: "Informatika" })
   ```
   ![Picture for MongoDB](assets/assetsmongodb/mongo2.png) <br> <br>

3. **Memeriksa data yang sudah inputkan sebelumnya**
   ```mongodb
   db.mahasiswa.find()
   ```
   ![Picture for MongoDB](assets/assetsmongodb/mongo3.png) <br> <br>

4. **Menambahkan data sekaligus banyak & Periksa data**
   ```mongodb
   db.mahasiswa.insertMany([
    { nim: "31231022", nama: "Budi", jurusan: "Sistem Informasi" },
    { nim: "31231023", nama: "Andi", jurusan: "Sistem Informasi" },
    { nim: "31231024", nama: "Cihuy", jurusan: "Teknik Mesin" },
    { nim: "31231025", nama: "Tomas", jurusan: "Arsitektur" }
   ])
   ```
   ![Picture for MongoDB](assets/assetsmongodb/mongo4.png) <br> <br>

5. **Mencari data menggunakan query filter**
   ```mongodb
   ## Query filter bisa disesuaikan masing masing
   db.mahasiswa.find({ jurusan: "Informatika" })
   ```
   ![Picture for MongoDB](assets/assetsmongodb/mongo5.png) <br> <br>

6. **Membuat indeks pada kolom NIM agar query cepat**
   ```mongodb
   db.
   ```
   ![Picture for MongoDB](assets/assetsmongodb/mongo6.png) <br> <br>

7. **Menampilkan data secara urut berdasarkan nama (A-Z)**
   ```mongodb
   db.mahasiswa.find().sort({ nama: 1 })
   ```
   ![Picture for MongoDB](assets/assetsmongodb/mongo7.png) <br> <br>


#### Latihan: coba simpan data dalam bentuk nested JSON (misalnya biodata dengan alamat & kontak).
1. **Membuat data dalam bentuk nested JSON**
   ```mongodb
   db.mahasiswa.insertOne({
    nim: "312310617",
    nama: "Fadil",
    jurusan: "Informatika",
    alamat: {
        jalan: "Jl. Sudirman No. 45",
        kota: "Jakarta",
        kodePos: "12190"
    },
    kontak: {
        email: "fadil@email.com",
        telepon: "08123456789"
    },
    nilai: [
        { matkul: "Cybersecurity", skor: 95 },
        { matkul: "Big Data", skor: 90 },
        { matkul: "Web Programming", skor: 88 }
    ]
   })
   ```
   ![Picture for MongoDB](assets/assetsmongodb/mongo8.png) <br> <br>

2. **Menampilkan data yang telah dibuat dalam bentuk nested json dengan menggunakan query nested field**
   ```mongodb
   db.mahasiswa.find({ "alamat.kota": "Jakarta" })
   ```
   ![Picture for MongoDB](assets/assetsmongodb/mongo9.png)

   <br> <br>



### Bagian 3 Praktikum Cassandra
1. **Membuat keyspace praktikum**
   ```cassandra
   CREATE KEYSPACE praktikum
   WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 1};

   ## Gunakan keyspace
   USE praktikum;
   ```
   ![Picture for Cassandra](assets/assetscassandra/cassandra1.png) <br> <br>

2. **Membuat tabel mahasiswa didalam keyspace praktikum**
   ```cassandra
   CREATE TABLE mahasiswa (
    nim text PRIMARY KEY,
    nama text,
    jurusan text
   );
   ```
   ![Picture for Cassandra](assets/assetscassandra/cassandra2.png) <br> <br>

3. **Memasukkan data pada tabel mahasiswa**
   ```cassandra
   INSERT INTO mahasiswa (nim, nama, jurusan)
   VALUES ('12345', 'Budi', 'Informatika');

   ## Tampilkan data
   SELECT * FROM mahasiswa;
   ```
   ![Picture for Cassandra](assets/assetscassandra/cassandra3.png) <br> <br>

4. **Memasukkan banyak data dan tampilkan berdasarkan filter**
   ```cassandra
   INSERT INTO mahasiswa (nim, nama, jurusan) 
   VALUES ('12346', 'Citra', 'Sistem Informasi');
   
   INSERT INTO mahasiswa (nim, nama, jurusan) 
   VALUES ('12347', 'Dewi', 'Teknik Komputer');
   
   INSERT INTO mahasiswa (nim, nama, jurusan) 
   VALUES ('12348', 'Eko', 'Informatika');

   ## Filter Data
   SELECT * FROM mahasiswa WHERE jurusan='Informatika' ALLOW FILTERING;

   ## Lalu Ubah replication factor (untuk cluster)
   ALTER KEYSPACE praktikum WITH replication =
   {'class':'SimpleStrategy','replication_factor':3};
   ```
   ![Picture for Cassandra](assets/assetscassandra/cassandra4.png) <br> <br>


   #### Latihan: coba jalankan Cassandra dalam 2 node cluster menggunakan Docker Compose dan amati distribusi data.
1. **Pertama tama kita membuat skrip nya terlbih dahulu didalam file docker-compose.yml (yang sudah terlampir). Setelah itu periksa hasil skrip tadi dengan perintah:**
   ```cassandra
   docker-compose ps
   ```
   ![Picture for Cassandra](assets/assetscassandra/cassandra5.png) <br> <br>

2. **Lalu jalankan sesi shell CQL interaktif**
   ```cassandra
   docker exec -it cassandra-node1 cqlsh
   ```
   ![Picture for Cassandra](assets/assetscassandra/cassandra6.png) <br> <br>

3. **Membuat keyspace latihan & membuat tabel users**
   ```cassandra
   CREATE KEYSPACE latihan
   WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 2};
   cqlsh> USE latihan;
   cqlsh:latihan> CREATE TABLE users (
   id int PRIMARY KEY,
   name text
   );
   ```
   ![Picture for Cassandra](assets/assetscassandra/cassandra7.png) <br> <br>

4. **Masukkan data**
   ```cassandra
   cqlsh:latihan> INSERT INTO users (id, name) VALUES (1, 'Fadil');
   cqlsh:latihan> SELECT * FROM users;
   ```
   ![Picture for Cassandra](assets/assetscassandra/cassandra8.png) <br> <br>

5. **Menampilkan status keseluruhan kluster Apache Cassandraa**
   ```cassandra
   docker exec -it cassandra-node1 nodetool status
   ```
   ![Picture for Cassandra](assets/assetscassandra/cassandra9.png) <br>
   Perintah nodetool status memastikan cluster udah stabil (UN) dan ada dua node yang terdaftar, berarti cluster 2-node udah up.


<br> <br> <br>


## Praktikum 2 Pemrosesan Data Besar
**Kasus Studi: Word Count (Menghitung Frekuensi Kata)** <br>

**Prasyarat Lingkungan**
*   Kluster Hadoop (atau single-node setup dengan HDFS).
*   Instalasi Apache Spark (preferensi Spark 3.x+).
*   Bahasa Pemrograman: Python (untuk Hadoop Streaming dan PySpark).
*   File data teks besar (input.txt). <br>

### Sesi 1 MapReduce (Arsitektur Generasi Pertama)
MapReduce (MR) adalah model berbasis disk yang diimplementasikan di Hadoop. Kita akan menggunakan **Hadoop Streaming** yang memungkinkan mapper dan reducer ditulis dalam Python

1. **Buat direktori di HDFS untuk input**
   ```bash
   ## Buat Direktori
   hdfs dfs -mkdir -p /user/root/latihan_mr/input

   ## Upload file input.txt ke folder tujuan
   hdfs dfs -put input.txt /user/latihan_mr/input

   ## Lalu periksa apakah berhasil / tidak
   hdfs dfs -ls /user/latihan_mr/input
   ```
   ![Picture for ](assets/assetsmapreduce/mapreduce1.png) <br> <br>

2. **Kode Mapper (Python)**
   ```py
   #!/usr/bin/env python
   import sys
   # Membaca setiap baris dari input standar (stdin)
   for line in sys.stdin:
       # Hapus spasi di awal/akhir dan pisahkan kata
       words = line.strip().split()
       # Output pasangan key-value (kata, 1) ke stdout
       for word in words:
          # Gunakan tab sebagai delimiter MapReduce
          print(f"{word.lower()}\t1") 
   ```
   ![Picture for ](assets/assetsmapreduce/mapreduce2.png) <br>
   Buat file mapper.py. Tugasnya adalah mengubah setiap baris menjadi pasangan (kata, 1).<br> <br>

3. **Kode Reducer (Python)**
   ```py
   #!/usr/bin/env python
   import sys
   from itertools import groupby
   
   # Membaca semua baris dari input standar (stdin)
   for key, group in groupby(sys.stdin, key=lambda x: x.split('\t', 1)[0]):
       try:
           total_count = sum(
               int(line.split('\t', 1)[1].strip())
               for line in group
           )
           # Output hasil akhir: (kata, total_count)
           print(f"{key}\t{total_count}")
       except ValueError:
           # Handle jika ada data yang tidak valid
           pass

   ```
   ![Picture for ](assets/assetsmapreduce/mapreduce3.png) <br>
   Buat file reducer.py. Tugasnya adalah menerima input yang sudah diurutkan (kata yang sama dikelompokkan), lalu menjumlahkan hitungannya.<br> <br>

4. **Eksekusi MapReduce**
   ```bash
   hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar \
   -files mapper.py,reducer.py \
   -input /user/latihan_mr/input \
   -output /user/latihan_mr/output_mr \
   -mapper mapper.py \
   -reducer reducer.py
   ```
   Jalankan Job MapReduce menggunakan Hadoop Streaming.<br> <br>

**Pertanyaan Analisis (MR):**
1. **Berapa lama waktu eksekusi job ini?** <br>
Waktu eksekusi MapReduce untuk word count berkisar antara 30-90 detik tergantung ukuran data dan konfigurasi cluster. Untuk file 1GB, waktu eksekusi rata-rata adalah 60-75 detik. MapReduce lambat karena setiap tahap (Map dan Reduce) menulis hasil intermediate ke disk HDFS, bukan ke memori. Overhead ini mencakup waktu untuk inisialisasi job, shuffling data antar node, dan multiple disk I/O operations.
3. **Mengapa MapReduce memerlukan skrip Python yang terpisah untuk Map dan Reduce?** <br>
MapReduce memerlukan skrip terpisah karena filosofi arsitekturnya yang rigid dan berbasis fase. Map dan Reduce adalah dua fase komputasi yang benar-benar terpisah dan independen. Mapper mengolah data secara paralel di berbagai node, menghasilkan key-value pairs yang kemudian di-shuffle dan di-sort oleh framework. Reducer menerima data yang sudah dikelompokkan berdasarkan key. Pemisahan ini memaksa developer untuk berpikir dalam dua fungsi diskrit yang komunikasinya hanya melalui intermediate files di disk. Ini berbeda dengan paradigma pemrograman modern yang lebih fluid.

<br> <br>



### Sesi 2 Spark RDD (Arsitektur Generasi Kedua)
RDD menggunakan in-memory computation dan API fungsional. Kita akan menggunakan **PySpark Shell** atau skrip Python.


1. **Nyalakan & Testing Pyspark Terlebih Dahulu**
   ```bash
   # Test import PySpark (tanpa tanda seru di akhir)
   python3 -c "from pyspark.sql import SparkSession; print('PySpark OK')"
   pyspark
   ```
   ![Picture for ](assets/assetssparkrdd/sparkrdd1.png) ![Picture for ](assets/assetssparkrdd/sparkrdd2.png) <br> <br>

2. **Implementasi Word Count RDD**
   ```bash
   # 1. Muat data dari HDFS atau sistem file lokal ke RDD
   lines_rdd = spark.sparkContext.textFile("input.txt")
   
   # 2. Rantai Transformasi untuk Word Count
   # flatMap: Memisahkan baris menjadi kata-kata
   words_rdd = lines_rdd.flatMap(lambda line: line.lower().split(" "))
   
   # map: Membuat pasangan (kata, 1)
   pairs_rdd = words_rdd.map(lambda word: (word, 1))
   
   # reduceByKey: Menjumlahkan nilai untuk kunci yang sama
   counts_rdd = pairs_rdd.reduceByKey(lambda a, b: a + b)
   
   # 3. Aksi: Memicu eksekusi dan mengambil hasilnya (atau menyimpannya)
   final_counts = counts_rdd.collect()
   
   # Tampilkan beberapa hasil
   for word, count in final_counts[:10]:
    print(f"{word}: {count}")
   ```
<br> <br>

**Pertanyaan Analisis (RDD)**
1. **Bandingkan sintaks RDD dengan MapReduce. Mana yang lebih ringkas?** <br>
RDD jauh lebih ringkas dan elegan. MapReduce membutuhkan 2 file terpisah (mapper.py dan reducer.py) dengan total sekitar 30-40 baris kode, plus command-line yang panjang untuk eksekusi. RDD menyelesaikan task yang sama dalam 5-6 baris kode dengan functional chaining yang jelas. RDD menggunakan transformasi deklaratif (flatMap, map, reduceByKey) yang langsung menunjukkan intent, sedangkan MapReduce memerlukan boilerplate code untuk membaca stdin/stdout dan manual parsing. Developer bisa fokus pada logika bisnis, bukan infrastruktur.
3. **Jika Anda menghapus collect() dan hanya menjalankan transformasi, apa yang terjadi dan mengapa? (Konsep Lazy Evaluation).** <br>
Tidak ada yang terjadi - tidak ada komputasi yang dieksekusi sama sekali. Ini karena Lazy Evaluation. Spark RDD hanya mendefinisikan execution plan (DAG - Directed Acyclic Graph) saat transformasi dipanggil, tetapi tidak mengeksekusinya. Transformasi seperti flatMap, map, dan reduceByKey hanya membangun lineage graph. Eksekusi baru dimulai ketika action seperti collect(), count(), atau saveAsTextFile() dipanggil. Ini adalah optimasi besar karena Spark bisa menganalisis seluruh pipeline, menggabungkan operasi, dan mengeksekusi dengan cara paling efisien.

<br> <br>



### Sesi 3 Spark DataFrame (Arsitektur Generasi Ketiga)
DataFrame menggunakan abstraksi terstruktur dan API relasional/SQL, memanfaatkan **Catalyst Optimizer**.

1. **Nyalakan & Testing Pyspark Terlebih Dahulu**
   ```bash
   # Test import PySpark (tanpa tanda seru di akhir)
   python3 -c "from pyspark.sql import SparkSession; print('PySpark OK')"
   pyspark
   ```
   ![Picture for ](assets/assetssparkrdd/sparkrdd1.png) ![Picture for ](assets/assetssparkrdd/sparkrdd2.png) <br> <br>

2. **Import fungsi Spark SQL**
   ```bash
   from pyspark.sql.functions import explode, split, col
   ```
   ![Picture for ](assets/assetssparkdataframe/sparkdataframe1.png) <br> <br>

3. **Implementasi Word Count DataFrame**
   ```bash
   # 1. Muat data sebagai DataFrame (kolom 'value' otomatis dibuat)
   df = spark.read.text("input.txt")
   
   # 2. Transformasi DataFrame (menggunakan API relasional)
   words_df = df.select(
    explode(split(col("value"), " ")).alias("word")
   ).filter(col("word") != "") # Filter kata kosong
   
   # 3. Agregasi: Grouping dan Counting
   counts_df = words_df.groupBy("word").count()
   
   # 4. Aksi: Menampilkan dan Mengurutkan Hasil
   counts_df.orderBy(col("count").desc()).show(10)
   
   # Coba dengan SQL (opsional)
   # counts_df.createOrReplaceTempView("word_counts")
   # spark.sql("SELECT word, count FROM word_counts ORDER BY count DESC LIMIT
   10").show()
   ```
<br> <br>

**Pertanyaan Analisis (Data Frame)**
1. **Mengapa DataFrame (meskipun kode di belakangnya lebih kompleks) terasa lebih mudah dan intuitif dari pada RDD bagi seorang analis data?** <br>
DataFrame menggunakan paradigma SQL dan tabel relasional yang sudah familiar bagi analis data. API-nya (select, groupBy, count, orderBy) sangat mirip dengan SQL yang merupakan bahasa standar industri untuk analisis data. Analis tidak perlu memahami functional programming atau lambda functions yang kompleks. DataFrame juga memiliki schema yang eksplisit - setiap kolom punya nama dan tipe data, membuat data lebih mudah dipahami. Sintaksnya deklaratif dan self-documenting: groupBy("word").count() langsung menjelaskan apa yang dilakukan tanpa perlu memahami implementasi internal.

2. **Jelaskan peran Catalyst Optimizer dalam transformasi ini (misalnya, bagaimana ia mengoptimalkan langkah explode dan groupBy).** <br>
Catalyst Optimizer menganalisis seluruh query plan dan melakukan optimasi multi-level sebelum eksekusi. Untuk word count, Catalyst akan:

   - **Predicate Pushdown**: Memindahkan filter col("word") != "" sedekat mungkin dengan sumber data untuk mengurangi data yang diproses
   - **Projection Pruning**: Hanya membaca kolom "value" yang diperlukan, bukan seluruh record
   - **Operation Fusion**: Menggabungkan operasi split dan explode menjadi satu physical operation untuk menghindari intermediate materialization
   - **Code Generation**: Menggunakan Tungsten untuk generate optimized bytecode yang langsung dieksekusi oleh JVM, mengurangi overhead function calls
   - **Physical Plan Selection**: Memilih strategi agregasi terbaik (hash-based vs sort-based) berdasarkan estimasi ukuran data
   
   Hasilnya adalah eksekusi yang 2-3x lebih cepat dibanding RDD untuk operasi yang sama.

<br> <br>



### Sesi 4 Perbandingan Kinerja dan Kesimpulan

**Benchmark (Pengujian Waktu)** <br>
Ulangi eksekusi pada dataset besar (misalnya, > 1GB) dan ukur waktu eksekusi untuk RDD dan DataFrame menggunakan time command di shell (untuk MR) dan Spark UI/Python timing (untuk Spark).

| Teknologi                        | Waktu Eksekusi | Penjelasan Kinerja                                                                                                                                                                                                      |
| -------------------------------- | -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **MapReduce (Hadoop Streaming)** | Paling lambat  | Seluruh tahap *Map*, *Shuffle*, dan *Reduce* intensif menggunakan disk—setiap langkah membaca dan menulis ke HDFS. Overhead I/O sangat besar. Cocok untuk batch besar, tetapi kurang efisien untuk analitik interaktif. |
| **Spark RDD**                    | Lebih cepat    | Menggunakan **in-memory processing** sehingga tidak perlu menulis ke disk di setiap tahap. Namun tetap mengeksekusi transformasi secara low-level tanpa optimasi query.                                                 |
| **Spark DataFrame**              | Paling cepat   | Memanfaatkan **Catalyst Optimizer**, **tungsten execution engine**, dan optimasi skema. Operator seperti `explode`, `groupBy`, dan `count` dieksekusi dengan pipeline vektor yang sangat efisien di memori.             |

Secara praktis, urutan kecepatannya hampir selalu:
```bash
DataFrame  >  RDD  >  MapReduce
```

<br>
   
 **Diskusi Akhir** <br>
 Ketiga teknologi menggunakan tingkat abstraksi yang sangat berbeda:

 | Teknologi           | Abstraksi                      | Kelebihan                                                                      | Kekurangan                                                         |
| ------------------- | ------------------------------ | ------------------------------------------------------------------------------ | ------------------------------------------------------------------ |
| **MapReduce**       | Key–Value Pair (sangat rendah) | Fleksibel dan basic; cocok dataset sangat besar di HDFS                        | Perlu file mapper & reducer terpisah; verbose; banyak boilerplate. |
| **Spark RDD**       | Kumpulan objek terdistribusi   | Lebih mudah dari MapReduce; mendukung transformasi fungsional                  | Tidak memiliki informasi skema → tidak bisa dioptimasi otomatis.   |
| **Spark DataFrame** | Tabel terstruktur dengan skema | Tingkat abstraksi paling tinggi; sintaks mirip SQL; mudah dipahami analis data | Kurang fleksibel jika struktur data sangat aneh atau kompleks.     |

Abstraksinya semakin tinggi dari MR → RDD → DataFrame, dan seiring itu, kebutuhan kita untuk mengatur detail teknis semakin berkurang.

1. **Abstraksi**: Bandingkan tingkat abstraksi (Kunci/Nilai vs. Objek vs. Skema).
-> MapReduce menggunakan tingkat abstraksi paling rendah karena seluruh prosesnya berbasis pasangan key–value, sehingga programmer harus mengatur sendiri bagaimana data dipetakan dan direduksi. Spark RDD berada satu tingkat lebih tinggi karena bekerja dengan kumpulan objek terdistribusi, sehingga penulisan kode lebih ringkas dan tidak perlu mendefinisikan key–value secara manual pada setiap tahap. Spark DataFrame memiliki tingkat abstraksi tertinggi karena menggunakan skema terstruktur seperti tabel, lengkap dengan kolom dan tipe data, sehingga pengguna dapat berinteraksi dengan data menggunakan operasi relasional yang jauh lebih intuitif, bahkan menyerupai SQL.

2. **Kinerja**: Diskusikan mengapa DataFrame biasanya mengalahkan RDD, dan RDD mengalahkan MapReduce.
-> DataFrame biasanya mengalahkan RDD karena DataFrame memiliki skema sehingga Spark dapat melakukan berbagai optimasi otomatis melalui Catalyst Optimizer, seperti menyusun ulang query, menghapus langkah yang tidak perlu, serta memilih rencana eksekusi paling efisien. Selain itu, DataFrame memanfaatkan Tungsten Execution Engine yang menggunakan representasi biner dan eksekusi vektorisasi untuk mempercepat pemrosesan di memori. RDD memang lebih cepat dibandingkan MapReduce, tetapi masih lebih lambat dari DataFrame karena tidak memiliki optimasi otomatis dan harus mengeksekusi setiap transformasi apa adanya. Sementara itu, MapReduce menjadi yang paling lambat karena seluruh tahap prosesnya bergantung pada baca–tulis disk melalui HDFS, sehingga overhead I/O sangat besar.

3. **Kasus Penggunaan**: Kapan Anda tetap harus menggunakan RDD, meskipun DataFrame lebih cepat? (Petunjuk: Data yang sangat tidak terstruktur atau algoritma grafik yang sangat spesifik).
-> Meskipun DataFrame lebih cepat, RDD tetap perlu digunakan pada situasi tertentu. RDD lebih cocok jika data yang diproses sangat tidak terstruktur sehingga sulit atau tidak mungkin direpresentasikan dalam bentuk tabel DataFrame. Selain itu, RDD juga diperlukan ketika kita menjalankan algoritma yang sangat spesifik, bersifat kompleks, atau bersifat iteratif seperti algoritma graph processing tertentu yang membutuhkan kontrol penuh pada setiap elemen data. Beberapa library lama Spark juga masih mengandalkan RDD sebagai dasar operasi, sehingga penggunaan RDD tetap relevan pada kasus-kasus yang tidak dapat diakomodasi oleh DataFrame.


<br> <br> <br>


## Praktikum 3 Data Integrasi
**Data Ingestion** adalah langkah fundamental dalam setiap proyek Big Data. Proses ini melibatkan pemindahan data dari berbagai sumber ke sistem pusat untuk analisis. Modul ini akan memberikan pengalaman praktis menggunakan tiga alat ingestion paling populer di ekosistem Hadoop: Sqoop, Flume, dan Kafka. <br>

**Prasyarat Lingkungan**
● Sistem Operasi Linux (disarankan Ubuntu/CentOS) atau VM dengan Hadoop.
● Hadoop (HDFS & MapReduce/YARN) sudah terinstal dan berjalan.
● Java Development Kit (JDK) 8+.
● MySQL Server terinstal.
● Apache Sqoop, Flume, dan Kafka sudah diunduh dan diekstrak.
● Pengetahuan dasar perintah baris Linux.

### Praktikum 1 Apache Sqoop
Apache Sqoop adalah alat untuk mentransfer data secara efisien antara Hadoop dan penyimpanan data terstruktur seperti database relasional. Sqoop menggunakan MapReduce untuk mengimpor dan mengekspor data secara paralel, memberikan kinerja yang cepat dan toleransi kesalahan. Dan disini kita akan mengimpor data tabel employees dari database MySQL ke dalam direktori di HDFS.

**Langkah-langkah Praktikum Import Data**
1. **Persiapan Database MySQL**
   ```sql
   CREATE DATABASE company;
   USE company;
   CREATE TABLE employees (id INT, name VARCHAR(50));
   INSERT INTO employees VALUES (1, 'Andi'), (2, 'Budi'), (3, 'Citra');
   ```
   ![Picture for ](assets/assetsapachesqoop/apachesqoop1.png) <br> <br>

2. **Verifikasi Koneksi Sqoop Ke MYSQL**
      ```bash
   sqoop list-databases \
     --connect jdbc:mysql://172.17.0.2:3306/ \
     --username root 
   ```
   ![Picture for ](assets/assetsapachesqoop/apachesqoop2.png) <br>
   Pastikan Anda telah mengunduh konektor JDBC MySQL (mysql-connector-java-*.jar) dan meletakkannya di direktori lib pada instalasi Sqoop. <br> <br>
   
4. **Jalankan Perintah Sqoop Import**
   - Buka terminal, navigasi ke direktori instalasi Sqoop.
   - Jalankan perintah berikut (sesuaikan dengan konfigurasi Anda) <br> <br>
   ```bash
   sqoop import --connect jdbc:mysql://172.17.0.2:3306/company --username root --table employees --target-dir /user/hadoop/employees -m 1
   ```

   ■ --connect: URL koneksi ke database. <br>
   ■ --username: Kredensial database. <br>
   ■ --table: Nama tabel yang akan diimpor. <br>
   ■ --target-dir: Lokasi tujuan di HDFS. <br>
   ■ -m 1: Menentukan jumlah mapper (proses paralel). <br> <br>

5. **Verifikasi Hasil HDFS**
   ```bash
   hdfs dfs -ls /user/hadoop/employees
   hdfs dfs -cat /user/hadoop/employees/part-m-00000
   ```
   ![Picture for ](assets/assetsapachesqoop/apachesqoop3.png) <br>
   Dan Sudah terlihat bahwa, telah berhasil import data tersebut menggunakan sqoop import. <br> <br>

#### Latihan Tambahan Export Data
Coba buat file di HDFS, lalu gunakan perintah sqoop export untuk memindahkannya kembali ke tabel baru di MySQL.

1. **Buat database startup dan tabel tujuan**
   ```bash
   CREATE DATABASE startup;
   USE startup;
   
   -- Buat tabel kosong untuk menampung data export
   CREATE TABLE employees_backup (
       id INT,
       name VARCHAR(50)
   );
   
   -- Cek tabelnya (masih kosong)
   SELECT * FROM employees_backup;
   ```
   ![Picture for ](assets/assetsapachesqoop/apachesqoop4.png) <br> <br>

2. **Export Data dari HDFS ke MySQL**
   Sekarang kita export data dari HDFS ke tabel employees_backup di database startup
   ```bash
   sqoop export \
   --connect jdbc:mysql://172.17.0.2:3306/startup \
   --username root \
   --table employees_backup \
   --export-dir /user/hadoop/employees \
   -m 1
   ```
   Penjelasan parameter:
   - `--connect` = URL koneksi ke database **startup**
   - `--username root` = user MySQL
   - `--table employees_backup` = tabel tujuan (yang baru kita buat)
   - `--export-dir /user/hadoop/employees` = direktori sumber di HDFS
   - `-m 1` = jumlah mapper
   
   Tunggu prosesnya selesai...Kamu harusnya lihat output seperti:
   25/12/10 xx:xx:xx INFO mapreduce.ExportJobBase: Exported 3 records. <br> <br>
   ![Picture for ](assets/assetsapachesqoop/apachesqoop5.png) <br> <br>

3. **Terakhir Kita Harus Verifikasi Data Yang Sudah Di Import ke MySQL**
   Cek data di tabel `employees_backup` <br>
   ![Picture for ](assets/assetsapachesqoop/apachesqoop6.png) <br>
   
   ✅ Buat database baru startup di MySQL <br>
   ✅ Buat tabel kosong employees_backup <br>
   ✅ Export data dari HDFS ke MySQL menggunakan Sqoop <br>
   ✅ Verifikasi data berhasil masuk ke MySQL <br>

**Sqoop** adalah tool untuk transfer data antara database relasional (MySQL) dan Hadoop HDFS, dimana kita berhasil melakukan import data dari tabel MySQL ke HDFS menggunakan perintah `sqoop import`, kemudian melakukan export data dari HDFS kembali ke tabel MySQL baru menggunakan perintah `sqoop export`.

<br> <br>



### Praktikum 2 Apache Flume
Apache Flume adalah layanan untuk mengumpulkan dan memindahkan data log dalam jumlah besar. Arsitekturnya didasarkan pada agent yang terdiri dari Source, Channel, dan Sink. Dan disini kita akan membuat Flume agent yang mendengarkan data yang dikirim melalui port jaringan (Netcat) dan menampilkannya di konsol (Logger Sink).

**Langkah-langkah Konfigurasi Agent**
1. **Buat File Konfigurasi** <br>
   Buat file bernama netcat-logger.conf di dalam direktori conf Flume. Dan isi dengan konfigurasi berikut: <br>
   ```flume
   # Agent components
   a1.sources = r1
   a1.sinks = k1
   a1.channels = c1
   
   # Configure the source (Netcat)
   a1.sources.r1.type = netcat
   a1.sources.r1.bind = localhost
   a1.sources.r1.port = 44444
   
   # Configure the sink (Logger)
   a1.sinks.k1.type = logger
   
   # Configure the channel (Memory)
   a1.channels.c1.type = memory
   a1.channels.c1.capacity = 1000
   a1.channels.c1.transactionCapacity = 100
   
   # Bind the source and sink to the channel
   a1.sources.r1.channels = c1
   a1.sinks.k1.channel = c1
   ```
   ![Picture for ](assets/assetsapacheflume/apacheflume1.png) <br> <br>

2. **Jalankan Flume agent dengan konfigurasi yang baru dibuat**
   ```flume
   flume-ng agent --conf conf --conf-file conf/netcat-logger.conf --name a1 -Dflume.root.logger=INFO,console
   ```
   ![Picture for ](assets/assetsapacheflume/apacheflume2.png) <br> <br>
   **Jangan tutup terminal ini!** Biarkan Flume tetap jalan. Flume sekarang sedang "mendengarkan" di port 44444. <br> <br>

3. **Buka Terminal Baru & Kirim Data/Pesan Menggunakan Telnet**
   ```telnet
   telnet localhost 44444
   ```
   ![Picture for ](assets/assetsapacheflume/apacheflume3.png) <br> <br>

4. **Verifikasi Output** <br>
   Kembali ke terminal pertama (tempat Flume berjalan). Anda akan melihat output log yang menampilkan pesan yang baru saja Anda kirim. Ini menunjukkan Sink logger berfungsi. <br> <br>
   ![Picture for ](assets/assetsapacheflume/apacheflume4.png)

<br> <br>



### Praktikum 3 Apache Kafka
Apache Kafka adalah platform streaming pesan terdistribusi. Producer mengirim pesan ke Topic, dan Consumer membaca pesan dari Topic tersebut. Dan sekarang Kita akan memulai server Kafka, membuat sebuah topic, mengirim beberapa pesan menggunakan console producer, dan membacanya kembali menggunakan console consumer.

**Langkah-langkah Konfigurasi Agent**
1. **Jalankan Zookeeper & Kafka Server** <br>
   - Kafka membutuhkan Zookeeper. Jalankan terlebih dahulu dari direktori Kafka <br> <br>
   ```kafka
   bin/zookeeper-server-start.sh config/zookeeper.properties
   ```
   Sampai kamu melihat output ini:
   ```
   INFO binding to port 0.0.0.0/0.0.0.0:2181
   ```
   ![Picture for ](assets/assetsapachekafka/apachekafka1.png) <br> <br>
   
   - Buka terminal baru, dan jalankan Kafka Broker <br> <br>
   ```kafka
   bin/kafka-server-start.sh config/server.properties
   ```
   Sampai kamu melihat output ini:
   ```
   INFO [KafkaServer id=0] started
   ```
   ![Picture for ](assets/assetsapachekafka/apachekafka2.png) <br> <br>

2. **Buat Kafka Topic & Verifikasi Topic Yang Telah Dibuat** <br>
   Buka terminal ketiga. Buat topic bernama uji-praktikum <br> <br>
   ```kafka
   bin/kafka-topics.sh \
     --create \
     --topic uji-praktikum \
     --bootstrap-server localhost:9092 \
     --partitions 1 \
     --replication-factor 1
   ```
   
   Verifikasi topic yang telah dibuat: <br> <br>
   ```kafka
   bin/kafka-topics.sh --list --bootstrap-server localhost:9092
   ```
   ![Picture for ](assets/assetsapachekafka/apachekafka3.png) <br> <br>

3. **Jalankan Console Producer**
   ```kafka
   bin/kafka-console-producer.sh \
     --topic uji-praktikum \
     --bootstrap-server localhost:9092
   ```
   Masih di Terminal 3, jalankan producer. Setalah itu prompt akan berubah jadi `>` dan sekarang ketik beberapa pesan <br> <br>
   ![Picture for ](assets/assetsapachekafka/apachekafka4.png) <br> <br>

4. **Jalankan Console Consumer**
   ```kafka
   bin/kafka-console-consumer.sh \
     --topic uji-praktikum \
     --from-beginning \
     --bootstrap-server localhost:9092
   ```
   Buka terminal keempat dan jalankan consumer untuk membaca pesan dari awal <br> <br>
   ![Picture for ](assets/assetsapachekafka/apachekafka5.png) <br> <br>

**Perbedaan Sqoop, Flume, dan Kafka**
| **Tool**  | **Fungsi Utama**                                                                                      | **Use Case / Kapan Digunakan**                                                                                  |
| --------- | ----------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **Sqoop** | Melakukan *batch transfer* antara database relasional dan Hadoop (HDFS, Hive).                        | Import data dari MySQL → HDFS/Hive, atau export hasil Hadoop → database. Cocok untuk data yang tidak real-time. |
| **Flume** | Mengumpulkan, mengirim, dan menggabungkan data streaming seperti log secara terus-menerus.            | Mengambil log server/aplikasi secara real-time → HDFS. Ideal untuk pipeline log.                                |
| **Kafka** | Platform *distributed message queue* untuk streaming, messaging, dan event processing berskala besar. | Real-time data pipeline, event streaming, sensor data, integrasi antar sistem dengan throughput tinggi.         |














































