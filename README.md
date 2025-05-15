# Laporan Proyek Machine Learning - Tiffany Widjaya

## Project Overview
_E-commerce_ didefinisikan oleh Whinston dan Kalakota sebagai “proses pembelian dan pemasaran informasi, barang, dan jasa melalui jaringan komputer, yang utamanya menggunakan internet”. Namun, istilah ini juga sering digunakan dalam konteks yang lebih luas, termasuk pemanfaatan teknologi internet seperti e-mail dan intranet untuk bertukar informasi baik di dalam organisasi maupun dengan pihak eksternal (Abdul Hussein et al., 2021). Situs belanja daring memiliki peran yang sangat penting dalam kehidupan sehari-hari kita, terutama karena manusia di abad ke-21 cenderung sangat sibuk dan produktif. Mayoritas orang berbelanja secara online, terutama dalam situasi pandemi COVID-19, dan mereka sangat bergantung pada ulasan atau cuitan teks sebelum membeli sesuatu untuk diri mereka sendiri. Hal ini dapat dianggap sebagai bagian integral dari kebiasaan manusia masa kini (Das et al., 2021).

Namun, permasalahan yang kerap muncul pada _platform e-commerce_ adalah sulitnya pengguna menemukan produk yang benar-benar relevan di antara ribuan bahkan jutaan pilihan yang tersedia. Kondisi ini menyebabkan pengguna harus menghabiskan lebih banyak waktu untuk mencari, atau bahkan meninggalkan platform karena kesulitan menemukan produk yang sesuai. Oleh karena itu, diperlukan sebuah sistem yang mampu memberikan rekomendasi secara otomatis dan personal, guna menyederhanakan proses pencarian dan meningkatkan kenyamanan dalam pengalaman berbelanja online.

Sistem rekomendasi produk, atau yang sering disebut _recommendation engine_, adalah sebuah alat yang bertugas untuk menghasilkan dan memberikan saran produk kepada pengguna tertentu berdasarkan aktivitas dan preferensi mereka sebelumnya. Mekanisme ini memungkinkan sistem untuk memahami kebiasaan dan kebutuhan pengguna dengan lebih dalam, serta menganalisis perilaku mereka saat menjelajahi _website_ toko atau _platform_ belanja _online_ (Smutek et al., 2024). Contohnya, ketika pengguna mencari suatu produk, sistem dapat menyarankan produk lain yang mirip atau bahkan lebih sesuai dengan kebutuhan mereka. Hal ini dapat meningkatkan kepuasan pengguna, konversi penjualan, serta membangun loyalitas pelanggan terhadap _platform_.

Menurut Abdul Hussien et al. (2021), sistem rekomendasi merupakan salah satu komponen utama dalam meningkatkan efisiensi, pengalaman pengguna, dan profitabilitas dalam sistem _e-commerce_ modern. Pendekatan seperti _Collaborative Filtering_ dan _Content-Based Filtering_ terbukti mampu membantu pengguna dalam menghadapi masalah *information overload* dan mempercepat proses pengambilan keputusan saat berbelanja _online_.

Dengan meningkatnya penggunaan _platform e-commerce_ seperti Shopee, Tokopedia, hingga Amazon, kebutuhan akan sistem rekomendasi yang cerdas dan adaptif menjadi semakin penting. Berdasarkan data dari Katadata (2023), Shopee mendominasi pasar _e-commerce_ Asia Tenggara dengan total kunjungan mencapai **55,1 miliar**, diikuti oleh Lazada dan TikTok Shop. Hal ini menunjukkan betapa kompetitifnya industri ini dan pentingnya menghadirkan pengalaman pengguna yang unggul, salah satunya melalui sistem rekomendasi yang personal.

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
- Memberikan nilai tambah bagi _platform e-commerce_ melalui personalisasi dan peningkatan keterlibatan pengguna.

### Solution Approach
Untuk mencapai tujuan di atas, digunakan dua pendekatan sistem rekomendasi:

1. **_Content-Based Filtering_ (CBF)**
Menggunakan deskripsi produk sebagai fitur utama, model ini menganalisis kemiripan antar produk berdasarkan TF-IDF dan _cosine similarity_. Cocok untuk pengguna baru (_cold-start_), karena tidak membutuhkan data interaksi dari user lain.
2. **_Collaborative Filtering_ (CF)**
Menggunakan data interaksi pengguna (riwayat pembelian) untuk merekomendasikan produk yang dibeli oleh user dengan pola yang mirip. Menggunakan pendekatan _user-based_ dengan algoritma _K-Nearest Neighbors_ dan _cosine similarity_ untuk mengukur kedekatan antar _user_.

Kedua pendekatan ini akan dievaluasi untuk melihat keefektifannya dalam konteks _e-commerce_ dan membantu menjawab permasalahan yang telah diidentifikasi.

---

## _Data Understanding_
_Dataset_ yang digunakan adalah data transaksi _e-commerce_ dari situs _retail online_ di kawasan Eropa, dengan fokus pada pelanggan dari United Kingdom.
_Dataset_ ini diperoleh dari _platform_ [Kaggle - E-Commerce Data](https://www.kaggle.com/datasets/carrie1/ecommerce-data).

_Dataset_ berisi 541.909 baris dan 8 kolom yang mencatat informasi penjualan produk selama tahun 2010–2011. Setelah pembersihan data (_cleaning_), jumlah _user_ unik yang digunakan adalah 4.338, dan jumlah produk unik adalah 3.665.

### Kondisi Data
Beberapa kondisi data sebelum _preprocessing_:

- Terdapat nilai kosong (_missing value_) di kolom `CustomerID` dan `Description`
- Terdapat transaksi dengan `Quantity` ≤ 0 dan `UnitPrice` ≤ 0
- Terdapat transaksi retur (ditandai dengan `InvoiceNo` diawali "C")
- Terdapat duplikat entri produk berdasarkan deskripsi (untuk kebutuhan CBF)

Semua kondisi tersebut telah ditangani pada tahap **_Data Preparation_**.

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

### _Exploratory Data Analysis_ (EDA)

Beberapa hasil eksplorasi awal terhadap data:

- **Produk Terlaris:** Produk seperti `"WHITE HANGING HEART T-LIGHT HOLDER"` dan `"REGENCY CAKESTAND 3 TIER"` memiliki volume penjualan tinggi.
- **Negara dengan Transaksi Terbanyak:** United Kingdom mendominasi transaksi, menjadikannya fokus utama sistem rekomendasi.
- **Distribusi Produk & Customer:**  
  - 4.338 _customer_ unik  
  - 3.665 produk unik

_Insight_ ini menjadi dasar penting dalam menentukan pendekatan sistem rekomendasi yang akan dibangun.

## _Data Preparation_

_Data preparation_ dilakukan untuk memastikan bahwa data yang digunakan bersih, valid, dan siap diproses oleh sistem rekomendasi. Semua tahapan dilakukan secara berurutan dan konsisten dengan _notebook_.

### 1. Menghapus Transaksi Retur

Transaksi yang merupakan retur ditandai dengan `InvoiceNo` yang diawali huruf "C". Data tersebut dihapus karena bukan merupakan pembelian aktual dan dapat memengaruhi kualitas sistem rekomendasi.

**Alasan:** Retur bukan representasi preferensi sebenarnya, dan dapat mengganggu model dalam memahami pola pembelian _user_.

### 2. Menghapus Nilai Tidak Valid

Baris data dengan `Quantity ≤ 0` dan `UnitPrice ≤ 0` dihapus karena dianggap tidak valid untuk transaksi pembelian.

**Alasan:** Produk yang dibeli dalam jumlah negatif atau harga nol kemungkinan adalah _error input_ atau transaksi tidak sah.

### 3. Menghapus Nilai Kosong (_Missing Value_)

Nilai kosong di kolom `CustomerID` dan `Description` dihapus karena dua kolom ini sangat krusial:
- `CustomerID` digunakan dalam pendekatan **Collaborative Filtering**
- `Description` digunakan dalam pendekatan **Content-Based Filtering**

### 4. Memfokuskan Data pada United Kingdom

Berdasarkan hasil EDA, lebih dari 90% transaksi berasal dari **United Kingdom**. Maka, sistem rekomendasi difokuskan pada _user_ dan produk dari negara ini untuk hasil yang lebih stabil dan representatif.
![Grafik Transaksi dari Berbagai Negara](https://github.com/user-attachments/assets/4ad11aa1-6a72-4ab9-a6a0-6385ab542377)


### 5. _Data Preparation_ untuk _Content-Based Filtering_ (CBF)

- Data produk dideduplikasi berdasarkan kolom `Description`
- Kolom `Description` diubah menjadi vektor numerik menggunakan **TF-IDF (_Term Frequency – Inverse Document Frequency_)**
- Similaritas antar produk dihitung menggunakan **_cosine similarity_**

**Alasan:** TF-IDF efektif dalam merepresentasikan makna deskriptif produk. _Cosine similarity_ digunakan untuk menghitung tingkat kemiripan antar produk.

### 6. _Data Preparation_ untuk _Collaborative Filtering_ (CF)

- Data diubah menjadi matriks interaksi `CustomerID x StockCode` menggunakan _pivot table_
- Nilai matriks diisi berdasarkan `Quantity`
- Model CF tidak menggunakan rating eksplisit, melainkan menggunakan **_implicit feedback_** dari jumlah pembelian
- Matriks digunakan untuk melatih model **_K-Nearest Neighbors_** dengan _cosine similarity_ sebagai ukuran kemiripan antar _user_

**Alasan:** Model CF memanfaatkan interaksi nyata antara _user_ dan produk untuk memberikan rekomendasi yang dipersonalisasi berdasarkan pola perilaku user lain.

## _Modeling_

Pada tahap ini, dibangun dua jenis sistem rekomendasi untuk menyelesaikan permasalahan dalam membantu pengguna menemukan produk yang relevan:

1. **_Content-Based Filtering_ (CBF)**
2. **_Collaborative Filtering_ (CF)**

### 1. _Content-Based Filtering_ (CBF)
Teknik _Content-Based Filtering_ (CBF) merekomendasikan item berdasarkan konten yang sebelumnya telah dilihat atau disukai oleh pengguna dalam profil mereka. Tujuan utama dari CBF adalah menyarankan objek atau konten yang mirip dengan apa yang disukai pengguna di masa lalu (Nudrat et al., 2022).

Pendekatan ini menggunakan kolom `Description` sebagai fitur utama.  
Deskripsi produk diubah menjadi representasi numerik menggunakan **TF-IDF**.  
Kemudian, kemiripan antar produk dihitung menggunakan **_cosine similarity_**.

#### Contoh _Top_-5 _Recommendation_ (Input: `"RED WOOLLY HOTTIE WHITE HEART."`)

| StockCode | Description                           |
|-----------|----------------------------------------|
| 90123D    | WHITE HEART OF GLASS BRACELET         |
| 23321     | SMALL WHITE HEART OF WICKER           |
| 84950B    | BLUE HOTTIE WHITE HEART               |
| 84950G    | GREEN HOTTIE WHITE HEART              |
| 84950R    | RED HOTTIE WHITE HEART                |

#### Visualisasi _Cosine Similarity_ antar Produk

Untuk memahami bagaimana sistem mengenali kemiripan antar produk berdasarkan deskripsi, dilakukan visualisasi **_cosine similarity_** pada 10 produk acak.

Gambar berikut menunjukkan **matriks kemiripan** antar produk berdasarkan nilai _cosine similarity_ dari vektor TF-IDF:

![Cosine Similarity antar Produk](https://github.com/user-attachments/assets/1a3be8b3-4f21-4bc1-b51f-2c6dda47debc)

- Warna biru lebih gelap menunjukkan kemiripan yang lebih tinggi (mendekati 1).
- Diagonal bernilai 1 karena setiap produk identik dengan dirinya sendiri.
- Misalnya, produk dengan deskripsi yang mengandung kata "HEART" atau "PINK" memiliki kemiripan yang lebih tinggi.

Visualisasi ini mendemonstrasikan bagaimana **_Content-Based Filtering_** memahami konteks teks deskripsi produk dan menghasilkan rekomendasi berbasis kemiripan semantik.

### 2. _Collaborative Filtering_ (CF)
_Collaborative Filtering_ (CF) memprediksi item yang relevan dengan berkolaborasi berdasarkan minat pengguna lain yang memiliki kesamaan. Sebagai contoh, pengguna A menyukai film bergenre aksi, dan pengguna B juga menyukai _genre_ yang sama. Maka, film aksi yang telah ditonton dan diberi rating oleh pengguna A akan direkomendasikan kepada pengguna B, selama pengguna B belum menontonnya (Nudrat et al., 2022).

Model ini menggunakan data interaksi _user_ dengan produk. Dibuat matriks `CustomerID x StockCode` berdasarkan jumlah pembelian (`Quantity`). Kemudian, digunakan model **_K-Nearest Neighbors_ (KNN)** untuk mencari _user_ dengan pola belanja serupa dan merekomendasikan produk dari tetangganya.

#### Contoh _Top_-5 _Recommendation_ untuk user 12748.0:

| StockCode | Description                          | Skor |
|-----------|---------------------------------------|------|
| 84947     | ANTIQUE SILVER TEA GLASS ENGRAVED    | 72   |
| 21495     | WHITE HANGING HEART T-LIGHT HOLDER   | 65   |
| 23358     | HOME BUILDING BLOCK WORD             | 59   |
| 20725     | LUNCH BAG RED RETROSPOT              | 56   |
| 22386     | JUMBO BAG PINK POLKADOT              | 53   |

### Perbandingan Pendekatan

| Aspek                    | _Content-Based Filtering_      | _Collaborative Filtering_         |
|-------------------------|------------------------------|----------------------------------|
| Data yang dibutuhkan     | Deskripsi produk             | Riwayat interaksi _user_-produk    |
| Kelebihan                | Tidak butuh data _user_ lain (cocok untuk _cold-start_ user) | Rekomendasi bersifat _personal_ dan lebih kontekstual |
| Kekurangan               | Kurang personal, bisa terbatas jika deskripsi kurang informatif | Tidak bisa memberi rekomendasi untuk _user_ atau produk baru |
| _Output_ yang dihasilkan   | Produk serupa berdasarkan konten | Produk yang dibeli _user_ serupa   |

Kedua pendekatan ini saling melengkapi:
- CBF unggul saat _user_ atau produk baru ditambahkan
- CF unggul saat data interaksi cukup kaya untuk mengenali pola

Proyek ini membandingkan performa keduanya untuk memahami kekuatan dan keterbatasan masing-masing dalam konteks _e-commerce_.

---

_## Evaluation_

Evaluasi dilakukan untuk mengukur seberapa relevan dan efektif sistem rekomendasi yang telah dibangun.  
Model dievaluasi menggunakan lima metrik utama yang umum digunakan pada sistem rekomendasi:

### Metrik Evaluasi

1. **_Precision_@K**  
   Mengukur proporsi _item_ yang relevan dalam top-K rekomendasi.  
   **Rumus:**  
   Precision@K = (Jumlah _item_ relevan dalam Top-K) / K

2. **_Recall_@K**  
   Mengukur seberapa banyak _item_ relevan yang berhasil ditemukan dari seluruh _item_ relevan yang tersedia.  
   **Rumus:**  
   Recall@K = (Jumlah _item_ relevan dalam Top-K) / (Jumlah _item_ relevan yang sebenarnya)

3. **F1-_Score_@K**  
   Rata-rata harmonik dari _precision_ dan _recall_. Berguna untuk menyeimbangkan kedua metrik.  
   **Rumus:**  
   F1@K = 2 × (_Precision_@K × _Recall_@K) / (_Precision_@K + _Recall_@K)

4. **_Mean Average Precision_ (MAP@K)**  
   Mengukur rata-rata presisi kumulatif pada setiap posisi item relevan dalam top-K.  
   **Rumus (sederhana):**  
   MAP@K = Rata-rata presisi pada posisi item relevan di semua user

5. **NDCG@K (_Normalized Discounted Cumulative Gain_)**  
   Mengukur relevansi item dalam hasil rekomendasi dengan memperhatikan posisinya.  
   Item yang relevan di posisi awal akan mendapat skor lebih tinggi.  
   **Rumus (sederhana):**  
   NDCG@K = DCG@K / IDCG@K  
   di mana DCG@K = Σ (rel_i / log2(i+1))

### Hasil Evaluasi

| Model                | _Precision_@5 | _Recall_@5 | F1@5   | MAP@5  | NDCG@5 |
|---------------------|-------------|----------|--------|--------|--------|
| _Content-Based_ (CBF) | 0.4         | 0.0011   | 0.0022 | 0.3333 | 0.5087 |
| _Collaborative_ (CF)  | 0.0         | 0.0      | 0.0    | 0.0    | 0.0    |

### Interpretasi Hasil

- **_Content-Based Filtering_** (CBF) menunjukkan performa stabil, dengan nilai precision dan NDCG yang cukup tinggi. Artinya, sistem mampu mengembalikan _item_ yang mirip dan relevan secara semantik.
- **_Collaborative Filtering_** (CF) gagal memberikan hasil yang relevan dalam kasus tertentu. Hal ini disebabkan oleh:
  - _User outlier_ (memiliki interaksi sangat banyak yang tidak mirip dengan _user_ lain)
  - _Sparsity_ data yang tinggi → sulit menemukan tetangga relevan
- Hasil ini tetap penting untuk ditampilkan karena merefleksikan **tantangan nyata dalam sistem rekomendasi**.

### Visualisasi Perbandingan Evaluasi Model

Grafik berikut menunjukkan perbandingan hasil evaluasi lima metrik utama antara model _Content-Based Filtering_ (CBF) dan _Collaborative Filtering_ (CF):
![Perbandingan Evaluasi CBF vs CF](https://github.com/user-attachments/assets/2e3931b5-25cf-4323-b566-c7acb4e8e17d)

- CBF memiliki nilai _precision_ dan NDCG yang jauh lebih tinggi dibanding CF.
- CF menghasilkan nilai 0 pada semua metrik karena tidak berhasil memberikan rekomendasi yang relevan pada _user outlier_.
- Visualisasi ini memperjelas perbedaan performa kedua model dalam konteks _dataset_ yang digunakan.

### Kesesuaian dengan _Problem_ dan _Goals_

Sistem yang dibangun berhasil:
- Menyediakan rekomendasi produk yang relevan (CBF)
- Membuktikan bahwa pendekatan berbeda punya kelebihan di kondisi berbeda
- Menjawab tantangan utama dalam _problem statement_, yaitu membantu pengguna menemukan produk yang sesuai secara efisien

## Daftar Pustaka
- Abdul Hussien, F. T., Rahma, A. M. S., & Abdul Wahab, H. B. (2021). Recommendation Systems for E-commerce Systems: An Overview. *Journal of Physics: Conference Series, 1897*(1), 012024. https://doi.org/10.1088/1742-6596/1897/1/012024
- Smutek, T., Kowalski, M., Ivashko, O., Chmura, R., & Sokołowska-Woźniak, J. (2024). A Graph-Based Recommendation System Leveraging Cosine Similarity for Enhanced Marketing Decisions. European Research Studies Journal, 27(Special Issue 2), 83–93.
- Nudrat, S., Khan, H. U., Iqbal, S., Talha, M. M., Alarfaj, F. K., & Almusallam, N. (2022). Users’ rating predictions using collaborating filtering based on users and items similarity measures. Computational Intelligence and Neuroscience, 2022, Article ID 2347641. https://doi.org/10.1155/2022/2347641
- Katadata. (2023, Agustus 28). Shopee Dominasi Pasar E-Commerce Asia Tenggara pada 2023. *Databoks Katadata*. https://databoks.katadata.co.id/teknologi-telekomunikasi/statistik/66989de7b7168/shopee-dominasi-pasar-e-commerce-asia-tenggara-pada-2023
