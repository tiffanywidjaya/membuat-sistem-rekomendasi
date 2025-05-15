# Laporan Proyek Machine Learning - Tiffany Widjaya

## Project Overview
E-commerce didefinisikan oleh Whinston dan Kalakota sebagai “proses pembelian dan pemasaran informasi, barang, dan jasa melalui jaringan komputer, yang utamanya menggunakan internet”. Namun, istilah ini juga sering digunakan dalam konteks yang lebih luas, termasuk pemanfaatan teknologi internet seperti email dan intranet untuk bertukar informasi baik di dalam organisasi maupun dengan pihak eksternal (Abdul Hussein et al., 2021). Situs belanja daring memiliki peran yang sangat penting dalam kehidupan sehari-hari kita, terutama karena manusia di abad ke-21 cenderung sangat sibuk dan produktif. Mayoritas orang berbelanja secara online, terutama dalam situasi pandemi Covid-19, dan mereka sangat bergantung pada ulasan atau cuitan teks sebelum membeli sesuatu untuk diri mereka sendiri. Hal ini dapat dianggap sebagai bagian integral dari kebiasaan manusia masa kini (Das et al., 2021).

Namun, permasalahan yang kerap muncul pada _platform e-commerce_ adalah sulitnya pengguna menemukan produk yang benar-benar relevan di antara ribuan bahkan jutaan pilihan yang tersedia. Kondisi ini menyebabkan pengguna harus menghabiskan lebih banyak waktu untuk mencari, atau bahkan meninggalkan platform karena kesulitan menemukan produk yang sesuai. Oleh karena itu, diperlukan sebuah sistem yang mampu memberikan rekomendasi secara otomatis dan personal, guna menyederhanakan proses pencarian dan meningkatkan kenyamanan dalam pengalaman berbelanja online.

Sistem rekomendasi produk, atau yang sering disebut _recommendation engine_, adalah sebuah alat yang bertugas untuk menghasilkan dan memberikan saran produk kepada pengguna tertentu berdasarkan aktivitas dan preferensi mereka sebelumnya. Mekanisme ini memungkinkan sistem untuk memahami kebiasaan dan kebutuhan pengguna dengan lebih dalam, serta menganalisis perilaku mereka saat menjelajahi _website_ toko atau platform belanja online (Smutek et al., 2024). Contohnya, ketika pengguna mencari suatu produk, sistem dapat menyarankan produk lain yang mirip atau bahkan lebih sesuai dengan kebutuhan mereka. Hal ini dapat meningkatkan kepuasan pengguna, konversi penjualan, serta membangun loyalitas pelanggan terhadap platform.

Menurut Abdul Hussien et al. (2021), sistem rekomendasi merupakan salah satu komponen utama dalam meningkatkan efisiensi, pengalaman pengguna, dan profitabilitas dalam sistem e-commerce modern. Pendekatan seperti Collaborative Filtering dan Content-Based Filtering terbukti mampu membantu pengguna dalam menghadapi masalah *information overload* dan mempercepat proses pengambilan keputusan saat berbelanja online.

Dengan meningkatnya penggunaan platform e-commerce seperti Shopee, Tokopedia, hingga Amazon, kebutuhan akan sistem rekomendasi yang cerdas dan adaptif menjadi semakin penting. Berdasarkan data dari Katadata (2023), Shopee mendominasi pasar e-commerce Asia Tenggara dengan total kunjungan mencapai **55,1 miliar**, diikuti oleh Lazada dan TikTok Shop. Hal ini menunjukkan betapa kompetitifnya industri ini dan pentingnya menghadirkan pengalaman pengguna yang unggul, salah satunya melalui sistem rekomendasi yang personal.

![Dominasi E-commerce Asia Tenggara](https://github.com/user-attachments/assets/4a1fdf3c-990e-43a5-bb55-b47c32982714)

Sumber: [Katadata Databoks - Shopee Dominasi E-Commerce 2023](https://databoks.katadata.co.id/teknologi-telekomunikasi/statistik/66989de7b7168/shopee-dominasi-pasar-e-commerce-asia-tenggara-pada-2023)

Maka dari itu, adanya sistem rekomendasi ini tidak hanya membantu pengguna menemukan produk yang sesuai minat mereka, namun juga menciptakan pengalaman belanja yang lebih personal dan efisien.

---

## Business Understanding

### Problem Statements
- **Pengguna kesulitan menemukan produk yang relevan di antara ribuan pilihan pada platform e-commerce.** Banyaknya variasi produk yang tersedia menyebabkan pengguna harus menghabiskan waktu lebih lama untuk mencari produk yang sesuai.
- **Pengguna cenderung melewatkan produk yang sebenarnya sesuai kebutuhan mereka karena keterbatasan navigasi dan tampilan.** Produk yang relevan bisa saja tidak muncul di halaman depan atau kategori yang sedang dijelajahi oleh user.
- **_Platform e-commerce_ membutuhkan sistem yang dapat meningkatkan personalisasi untuk mempertahankan loyalitas pengguna dan meningkatkan konversi penjualan.**

### Goals
- Membangun sistem rekomendasi yang mampu memberikan saran produk yang sesuai dengan preferensi pengguna.
- Meningkatkan kenyamanan dan efisiensi pengalaman pengguna dalam mencari produk yang relevan.
- Memberikan nilai tambah bagi platform e-commerce melalui personalisasi dan peningkatan keterlibatan pengguna.

### Solution Approach
Untuk mencapai tujuan di atas, digunakan dua pendekatan sistem rekomendasi:

1. **Content-Based Filtering (CBF)**
Menggunakan deskripsi produk sebagai fitur utama, model ini menganalisis kemiripan antar produk berdasarkan TF-IDF dan cosine similarity. Cocok untuk pengguna baru (cold-start), karena tidak membutuhkan data interaksi dari user lain.
2. **Collaborative Filtering (CF)**
Menggunakan data interaksi pengguna (riwayat pembelian) untuk merekomendasikan produk yang dibeli oleh user dengan pola yang mirip. Menggunakan pendekatan user-based dengan algoritma K-Nearest Neighbors dan cosine similarity untuk mengukur kedekatan antar user.

Kedua pendekatan ini akan dievaluasi untuk melihat keefektifannya dalam konteks e-commerce dan membantu menjawab permasalahan yang telah diidentifikasi.

---

## Data Understanding
Dataset yang digunakan adalah data transaksi e-commerce dari situs retail online di kawasan Eropa, dengan fokus pada pelanggan dari United Kingdom.
Dataset ini diperoleh dari platform [Kaggle - E-Commerce Data](https://www.kaggle.com/datasets/carrie1/ecommerce-data).

Dataset berisi 541.909 baris dan 8 kolom yang mencatat informasi penjualan produk selama tahun 2010–2011. Setelah pembersihan data (cleaning), jumlah user unik yang digunakan adalah 4.338, dan jumlah produk unik adalah 3.665.

### Kondisi Data
Beberapa kondisi data sebelum preprocessing:

- Terdapat nilai kosong (missing value) di kolom `CustomerID` dan `Description`
- Terdapat transaksi dengan `Quantity` ≤ 0 dan `UnitPrice` ≤ 0
- Terdapat transaksi retur (ditandai dengan `InvoiceNo` diawali "C")
- Terdapat duplikat entri produk berdasarkan deskripsi (untuk kebutuhan CBF)

Semua kondisi tersebut telah ditangani pada tahap **Data Preparation**.

### Uraian Fitur

| Fitur        | Tipe Data   | Deskripsi                                                                 |
|--------------|-------------|--------------------------------------------------------------------------|
| `InvoiceNo`  | object      | ID transaksi, jika diawali "C" berarti transaksi retur                  |
| `StockCode`  | object      | Kode produk                                                              |
| `Description`| object      | Nama atau deskripsi produk                                               |
| `Quantity`   | integer     | Jumlah produk yang dibeli                                                |
| `InvoiceDate`| datetime    | Tanggal dan waktu transaksi terjadi                                      |
| `UnitPrice`  | float       | Harga satuan produk (dalam Poundsterling)                                |
| `CustomerID` | float       | ID unik pelanggan                                                        |
| `Country`    | object      | Negara asal transaksi                                                    |

### Exploratory Data Analysis (EDA)

Beberapa hasil eksplorasi awal terhadap data:

- **Produk Terlaris:** Produk seperti `"WHITE HANGING HEART T-LIGHT HOLDER"` dan `"REGENCY CAKESTAND 3 TIER"` memiliki volume penjualan tinggi.
- **Negara dengan Transaksi Terbanyak:** United Kingdom mendominasi transaksi, menjadikannya fokus utama sistem rekomendasi.
- **Distribusi Produk & Customer:**  
  - 4.338 customer unik  
  - 3.665 produk unik

Insight ini menjadi dasar penting dalam menentukan pendekatan sistem rekomendasi yang akan dibangun.

## Data Preparation

Data preparation dilakukan untuk memastikan bahwa data yang digunakan bersih, valid, dan siap diproses oleh sistem rekomendasi. Semua tahapan dilakukan secara berurutan dan konsisten dengan notebook.


### 1. Menghapus Transaksi Retur

Transaksi yang merupakan retur ditandai dengan `InvoiceNo` yang diawali huruf "C". Data tersebut dihapus karena bukan merupakan pembelian aktual dan dapat memengaruhi kualitas sistem rekomendasi.

**Alasan:** Retur bukan representasi preferensi sebenarnya, dan dapat mengganggu model dalam memahami pola pembelian user.

### 2. Menghapus Nilai Tidak Valid

Baris data dengan `Quantity ≤ 0` dan `UnitPrice ≤ 0` dihapus karena dianggap tidak valid untuk transaksi pembelian.

**Alasan:** Produk yang dibeli dalam jumlah negatif atau harga nol kemungkinan adalah error input atau transaksi tidak sah.

### 3. Menghapus Nilai Kosong (Missing Value)

Nilai kosong di kolom `CustomerID` dan `Description` dihapus karena dua kolom ini sangat krusial:
- `CustomerID` digunakan dalam pendekatan **Collaborative Filtering**
- `Description` digunakan dalam pendekatan **Content-Based Filtering**

### 4. Memfokuskan Data pada United Kingdom

Berdasarkan hasil EDA, lebih dari 90% transaksi berasal dari **United Kingdom**. Maka, sistem rekomendasi difokuskan pada user dan produk dari negara ini untuk hasil yang lebih stabil dan representatif.

### 5. Data Preparation untuk Content-Based Filtering (CBF)

- Data produk dideduplikasi berdasarkan kolom `Description`
- Kolom `Description` diubah menjadi vektor numerik menggunakan **TF-IDF (Term Frequency – Inverse Document Frequency)**
- Similaritas antar produk dihitung menggunakan **cosine similarity**

**Alasan:** TF-IDF efektif dalam merepresentasikan makna deskriptif produk. Cosine similarity digunakan untuk menghitung tingkat kemiripan antar produk.

### 6. Data Preparation untuk Collaborative Filtering (CF)

- Data diubah menjadi matriks interaksi `CustomerID x StockCode` menggunakan pivot table
- Nilai matriks diisi berdasarkan `Quantity`
- Model CF tidak menggunakan rating eksplisit, melainkan menggunakan **implicit feedback** dari jumlah pembelian
- Matriks digunakan untuk melatih model **K-Nearest Neighbors** dengan cosine similarity sebagai ukuran kemiripan antar user

**Alasan:** Model CF memanfaatkan interaksi nyata antara user dan produk untuk memberikan rekomendasi yang dipersonalisasi berdasarkan pola perilaku user lain.

## Modeling

Pada tahap ini, dibangun dua jenis sistem rekomendasi untuk menyelesaikan permasalahan dalam membantu pengguna menemukan produk yang relevan:

1. **Content-Based Filtering (CBF)**
2. **Collaborative Filtering (CF)**

### 1. Content-Based Filtering (CBF)

Pendekatan ini menggunakan kolom `Description` sebagai fitur utama.  
Deskripsi produk diubah menjadi representasi numerik menggunakan **TF-IDF**.  
Kemudian, kemiripan antar produk dihitung menggunakan **cosine similarity**.

#### Contoh Top-5 Recommendation (Input: `"RED WOOLLY HOTTIE WHITE HEART."`)

| StockCode | Description                           |
|-----------|----------------------------------------|
| 90123D    | WHITE HEART OF GLASS BRACELET         |
| 23321     | SMALL WHITE HEART OF WICKER           |
| 84950B    | BLUE HOTTIE WHITE HEART               |
| 84950G    | GREEN HOTTIE WHITE HEART              |
| 84950R    | RED HOTTIE WHITE HEART                |

#### Visualisasi Cosine Similarity antar Produk

Untuk memahami bagaimana sistem mengenali kemiripan antar produk berdasarkan deskripsi, dilakukan visualisasi **cosine similarity** pada 10 produk acak.

Gambar berikut menunjukkan **matriks kemiripan** antar produk berdasarkan nilai cosine similarity dari vektor TF-IDF:

![Cosine Similarity antar Produk](https://github.com/user-attachments/assets/1a3be8b3-4f21-4bc1-b51f-2c6dda47debc)

- Warna biru lebih gelap menunjukkan kemiripan yang lebih tinggi (mendekati 1).
- Diagonal bernilai 1 karena setiap produk identik dengan dirinya sendiri.
- Misalnya, produk dengan deskripsi yang mengandung kata "HEART" atau "PINK" memiliki kemiripan yang lebih tinggi.

Visualisasi ini mendemonstrasikan bagaimana **Content-Based Filtering** memahami konteks teks deskripsi produk dan menghasilkan rekomendasi berbasis kemiripan semantik.

### 2. Collaborative Filtering (CF)

Model ini menggunakan data interaksi user dengan produk.  
Dibuat matriks `CustomerID x StockCode` berdasarkan jumlah pembelian (`Quantity`).  
Kemudian, digunakan model **K-Nearest Neighbors (KNN)** untuk mencari user dengan pola belanja serupa dan merekomendasikan produk dari tetangganya.

#### Contoh Top-5 Recommendation untuk user 12748.0:

| StockCode | Description                          | Skor |
|-----------|---------------------------------------|------|
| 84947     | ANTIQUE SILVER TEA GLASS ENGRAVED    | 72   |
| 21495     | WHITE HANGING HEART T-LIGHT HOLDER   | 65   |
| 23358     | HOME BUILDING BLOCK WORD             | 59   |
| 20725     | LUNCH BAG RED RETROSPOT              | 56   |
| 22386     | JUMBO BAG PINK POLKADOT              | 53   |

### Perbandingan Pendekatan

| Aspek                    | Content-Based Filtering      | Collaborative Filtering         |
|-------------------------|------------------------------|----------------------------------|
| Data yang dibutuhkan     | Deskripsi produk             | Riwayat interaksi user-produk    |
| Kelebihan                | Tidak butuh data user lain (cocok untuk cold-start user) | Rekomendasi bersifat personal dan lebih kontekstual |
| Kekurangan               | Kurang personal, bisa terbatas jika deskripsi kurang informatif | Tidak bisa memberi rekomendasi untuk user atau produk baru |
| Output yang dihasilkan   | Produk serupa berdasarkan konten | Produk yang dibeli user serupa   |

Kedua pendekatan ini saling melengkapi:
- CBF unggul saat user atau produk baru ditambahkan
- CF unggul saat data interaksi cukup kaya untuk mengenali pola

Proyek ini membandingkan performa keduanya untuk memahami kekuatan dan keterbatasan masing-masing dalam konteks e-commerce.

---

## Evaluation

Evaluasi dilakukan untuk mengukur seberapa relevan dan efektif sistem rekomendasi yang telah dibangun.  
Model dievaluasi menggunakan lima metrik utama yang umum digunakan pada sistem rekomendasi:

### Metrik Evaluasi

1. **Precision@K**  
   Mengukur proporsi item yang relevan dalam top-K rekomendasi.  
   **Rumus:**  
   Precision@K = (Jumlah item relevan dalam Top-K) / K

2. **Recall@K**  
   Mengukur seberapa banyak item relevan yang berhasil ditemukan dari seluruh item relevan yang tersedia.  
   **Rumus:**  
   Recall@K = (Jumlah item relevan dalam Top-K) / (Jumlah item relevan yang sebenarnya)

3. **F1-Score@K**  
   Rata-rata harmonik dari precision dan recall. Berguna untuk menyeimbangkan kedua metrik.  
   **Rumus:**  
   F1@K = 2 × (Precision@K × Recall@K) / (Precision@K + Recall@K)

4. **Mean Average Precision (MAP@K)**  
   Mengukur rata-rata presisi kumulatif pada setiap posisi item relevan dalam top-K.  
   **Rumus (sederhana):**  
   MAP@K = Rata-rata presisi pada posisi item relevan di semua user

5. **NDCG@K (Normalized Discounted Cumulative Gain)**  
   Mengukur relevansi item dalam hasil rekomendasi dengan memperhatikan posisinya.  
   Item yang relevan di posisi awal akan mendapat skor lebih tinggi.  
   **Rumus (sederhana):**  
   NDCG@K = DCG@K / IDCG@K  
   di mana DCG@K = Σ (rel_i / log2(i+1))

### Hasil Evaluasi

| Model                | Precision@5 | Recall@5 | F1@5   | MAP@5  | NDCG@5 |
|---------------------|-------------|----------|--------|--------|--------|
| Content-Based (CBF) | 0.4         | 0.0011   | 0.0022 | 0.3333 | 0.5087 |
| Collaborative (CF)  | 0.0         | 0.0      | 0.0    | 0.0    | 0.0    |

### Interpretasi Hasil

- **Content-Based Filtering** (CBF) menunjukkan performa stabil, dengan nilai precision dan NDCG yang cukup tinggi. Artinya, sistem mampu mengembalikan item yang mirip dan relevan secara semantik.
- **Collaborative Filtering** (CF) gagal memberikan hasil yang relevan dalam kasus tertentu. Hal ini disebabkan oleh:
  - User outlier (memiliki interaksi sangat banyak yang tidak mirip dengan user lain)
  - Sparsity data yang tinggi → sulit menemukan tetangga relevan
- Hasil ini tetap penting untuk ditampilkan karena merefleksikan **tantangan nyata dalam sistem rekomendasi**.

### Kesesuaian dengan Problem dan Goals

Sistem yang dibangun berhasil:
- Menyediakan rekomendasi produk yang relevan (CBF)
- Membuktikan bahwa pendekatan berbeda punya kelebihan di kondisi berbeda
- Menjawab tantangan utama dalam problem statement, yaitu membantu pengguna menemukan produk yang sesuai secara efisien

## Daftar Pustaka
- Abdul Hussien, F. T., Rahma, A. M. S., & Abdul Wahab, H. B. (2021). Recommendation Systems for E-commerce Systems: An Overview. *Journal of Physics: Conference Series, 1897*(1), 012024. https://doi.org/10.1088/1742-6596/1897/1/012024
- Smutek, T., Kowalski, M., Ivashko, O., Chmura, R., & Sokołowska-Woźniak, J. (2024). A Graph-Based Recommendation System Leveraging Cosine Similarity for Enhanced Marketing Decisions. European Research Studies Journal, 27(Special Issue 2), 83–93.
