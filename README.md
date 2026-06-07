# Bali Tourism End-to-End Business Intelligence Project

## 📌 Overview
Proyek ini merupakan solusi *Business Intelligence* (BI) dan *Data Engineering end-to-end* yang dirancang untuk menganalisis pendapatan serta kinerja ekonomi sektor pariwisata di wilayah Provinsi Bali. Solusi ini mengintegrasikan data dari database transaksional (OLTP) melalui pipa ETL ke dalam *Data Warehouse* berbasis *Star Schema*, membangun *OLAP Multidimensional Cube* untuk kebutuhan analisis mendalam (*slicing & dicing*), dan menyajikannya dalam bentuk *Executive Dashboard* interaktif.

---

## 🛠️ Tech Stack
* **Database Engine & DWH:** Microsoft SQL Server
* **ETL Engine:** SQL Server Integration Services (SSIS)
* **OLAP Engine:** SQL Server Analysis Services (SSAS) - Multidimensional Cube
* **Query Languages:** T-SQL & MDX (*Multi-Dimensional eXpressions*)
* **BI Visualization Tool:** Tableau

---

## 📂 Repository Structure
Berkas di dalam repositori ini disusun dengan struktur berikut:
* `database/`: Berisi script `.sql` skema database transaksional (OLTP) dan rancangan tabel *Data Warehouse* (DW).
* `etl-integration-services/`: Berisi paket kompilasi Visual Studio untuk pipeline ETL menggunakan SSIS.
* `olap-analysis-services/`: Berisi proyek Visual Studio untuk pengembangan arsitektur OLAP Multidimensional Cube.
* `reports-and-documentations/`: Berisi dokumen laporan teknis lengkap proyek dalam format PDF.
* `.gitignore`: Berkas konfigurasi untuk mengabaikan file biner/build lokal (`bin/`, `obj/`, `*.user`).

---

## 📐 Data Warehouse Architecture & Dimensional Modeling

### 1. Conceptual Star Schema
Data Warehouse dimodelkan menggunakan **Star Schema** yang terdiri dari **1 Tabel Fakta (Fact Table)** dan **4 Tabel Dimensi (Dimension Tables)**:

* **Fact Table:** `Fact_Tourism`
  * *Measures:* `Total_Revenue_IDR`, `Accommodation_Revenue_IDR`, `FNB_Revenue_IDR`, `Activities_Revenue_IDR`, `Tax_Amount_IDR`, `Number_of_Guests`.
* **Dimension Tables:**
  * `Dim_Destination`: Menyimpan data objek wisata beserta kategori dan lokasi kotanya.
  * `Dim_Hotel`: Menyimpan profil hotel, kelas bintang, dan kategori harganya.
  * `Dim_Tourist`: Menyimpan profil asal negara turis, kelompok usia, dan jenis perjalanannya.
  * `Dim_Time`: Menyimpan dimensi waktu kuartal, bulan, hingga penanda musim kunjungan.

### 2. Information Package & Hierarchy
Mendukung analisis data berbasis hirarki berikut untuk kebutuhan agregasi (*Drill-Down* / *Roll-Up*):
* **Waktu:** Year ➡️ Semester ➡️ Quarter ➡️ Month.
* **Wisatawan:** Jenis Wisatawan ➡️ Negara Asal ➡️ Age Group ➡️ Travel Type.
* **Destinasi:** Nama Destinasi ➡️ Kota ➡️ Provinsi ➡️ Kategori Destinasi.
* **Akomodasi:** Nama Hotel ➡️ Kelas Hotel ➡️ Kota ➡️ Price Kategori.

---

## 🔄 ETL (Extraction, Transformation, Loading) Pipelines
Pipeline data dibangun menggunakan **SSIS** dengan menerapkan dua metode penyegaran data demi menjaga efisiensi dan integritas data:

1. **Full Refresh Pipeline:** Dijalankan secara berurutan (*Sequence Container*) dengan membersihkan (*Clean Up*) data fakta terlebih dahulu sebelum data dimensi untuk mematuhi aturan *Referential Integrity* database. Selanjutnya dilakukan ekstraksi, transformasi agregasi (*Pivot*), serta pencarian kunci induk (*Lookup Key*).
2. **Incremental Refresh & SCD Type 2:** Diterapkan khusus pada dimensi turis (`Dim_Tourist`) menggunakan komponen **Slowly Changing Dimension (SCD) Tipe 2**. Mekanisme ini melacak riwayat perubahan profil perilaku wisatawan dari waktu ke waktu secara historis dengan memanfaatkan penanda kolom aktif (`Is_Current`), `Start_Date`, dan `End_Date`.

---

## 📊 OLAP Multidimensional Cube & MDX Queries
Membangun proyek *Analysis Services* (SSAS) untuk menyebarkan basis data multidimensional. Evaluasi performa bisnis divalidasi dengan mengeksekusi *Query* MDX pada SSMS untuk menjawab kebutuhan strategis berikut:

* **Analisis Preferensi Pasar (Slicing/Dicing):** Menghitung total pengeluaran dan jumlah tamu berdasarkan kategori destinasi di tiap kuartal.
* **Analisis Spending Behavior:** Membandingkan kontribusi pendapatan kamar hotel (*Accommodation*) dengan pengeluaran makanan/minuman (*F&B*) berdasarkan tipe perjalanan tamu.
* **Analisis Pasar Premium (Slicing Terarah):** Melakukan filtrasi *Where Clause* spesifik untuk mengisolasi pendapatan aktivitas pariwisata dari turis asal Australia yang menginap di hotel bintang 5 sepanjang tahun 2024.

---

## 📈 Executive Dashboard & Business Insights
Melalui visualisasi interaktif menggunakan Tableau, manajemen dapat menarik beberapa rekomendasi bisnis yang berbasis data (*Data-Driven*):

* **Segmentasi Budaya vs Alam:** Wisata Budaya didominasi kuat oleh pasar internasional dengan puncak pendapatan terbesar berada di Kuartal 2 (Q2). Sebaliknya, wisatawan domestik jauh lebih condong memilih kategori Wisata Alam. Promosi disarankan terfokus 80-90% sesuai preferensi pasar ini.
* **Stabilitas Sektor Pantai:** Wisata Pantai bertindak sebagai komponen "tulang punggung" ekonomi pariwisata daerah karena menghasilkan aliran pendapatan tinggi yang paling stabil di setiap kuartal bagi kedua jenis pasar.
* **Optimalisasi Revenue Hotel Premium:** Tamu di hotel bintang 5 (didominasi tipe *Couple* dan *Family*) terbukti sangat royal membelanjakan uangnya untuk layanan kamar dan restoran (*F&B* mencapai kontribusi hingga 30%). Namun, mereka cenderung pasif melakukan aktivitas luar hotel, memberikan peluang bagi manajemen untuk menciptakan paket aktivitas eksklusif khusus dari dalam hotel.
