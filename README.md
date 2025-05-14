# Sistem Rekomendasi Buku – Dicoding Submission

## Project Overview

Sistem rekomendasi adalah salah satu komponen penting dalam platform digital modern, terutama untuk membantu pengguna menemukan item yang relevan dari jumlah pilihan yang sangat besar.

Proyek ini bertujuan untuk membangun sistem rekomendasi buku berdasarkan dataset dari Kaggle yang mencakup informasi pengguna, buku, dan rating yang diberikan. Sistem ini menggunakan dua pendekatan utama:
- Content-Based Filtering
- Collaborative Filtering

---

## Business Understanding

### Problem Statement
Banyak pengguna kesulitan menemukan buku yang sesuai dengan preferensi mereka di tengah jumlah pilihan yang sangat banyak.

### Goals
Membangun sistem rekomendasi yang mampu menyarankan buku yang relevan, baik berdasarkan kemiripan konten maupun preferensi pengguna lain.

### Solution Approach
Sistem menggunakan:
1. **Content-Based Filtering** – Fokus pada informasi judul & penulis buku.
2. **Collaborative Filtering (SVD)** – Fokus pada interaksi user–item (rating).

---

## Data Understanding
Sistem rekomendasi ini menggunakan 3 dataset utama yang bersumber dari Kaggle:

Sumber data: [Books Dataset](https://www.kaggle.com/datasets/saurabhbagchi/books-dataset)

Dataset terdiri dari:
- **Books.csv** → ISBN, Title, Author, Year, Publisher
- **Users.csv** → User-ID, Age
- **Ratings.csv** → User-ID, ISBN, Rating

### 1. ```Books.csv```
**Jumlah Baris & Kolom:**
Baris: 271.379
Kolom: 5
**Kondisi data:**
  - Terdapat missing value pada `Author`, `Publisher`, dan `Year`
  - Ditemukan outlier pada `Year`, seperti nilai `0`, tahun < 1000 atau > 2025
  - Beberapa duplikasi ditemukan pada `Title`, diatasi dengan `.drop_duplicates(subset='Title')`
**Uraian fitur:**
| Fitur              | Deskripsi                                       |
|--------------------|-------------------------------------------------|
| `ISBN`             | Kode buku unik (string)                         |
| `Title`            | Judul buku                                      |
| `Author`           | Penulis buku                                    |
| `Year`             | Tahun terbit                                    |
| `Publisher`        | Penerbit                                        |

### 2. ```Users.csv```
**Jumlah Baris & Kolom:**
Baris: 278,859
Kolom: 3
**Kondisi Data:**
- Terjadi **missing value** pada kolom ```Age```.
- Tidak ditemukan duplikat ```User-ID```.
**Uraian fitur**:
| Fitur      | Deskripsi                                      |
|------------|------------------------------------------------|
| `User-ID`  | ID unik pengguna                               |
| `Age`      | Usia pengguna                                  |

### 3. ```Ratings.csv```
Jumlah Baris & Kolom:
Baris: 1,048,575
Kolom: 3
Kondisi Data:
- Sebagian besar rating adalah eksplisit (0–10).
- Tidak terdapat missing value.
- Terdapat ```User-ID``` yang memberikan rating sangat banyak, digunakan untuk testing collaborative filtering.
**Uraian fitur**:
| Fitur      | Deskripsi                                      |
|------------|------------------------------------------------|
| `User-ID`  | ID unik pengguna                               |
| `ISBN`     | Kode buku unik                                 |
| `Rating`   | Nilai rating eksplisit (0–10)                  |

### Distribusi Usia Pengguna
Mayoritas pengguna berada dalam rentang usia 20-40 tahun.

### User Paling Aktif
User-ID `11676` memberikan 13,602 rating – kandidat ideal untuk pengujian collaborative filtering.

### Buku Populer
ISBN `0971880107` mendapat 2,502 rating – buku paling sering dinilai.

---

## Data Preparation

### 1. Konversi Tipe Data
- Kolom `Age` dikonversi ke numerik menggunakan `pd.to_numeric(errors='coerce')`

### 2. Handling Duplicates
- Duplikasi berdasarkan `Title` dihapus sebelum sampling menggunakan `.drop_duplicates(subset='Title')`

### 3. Sampling Data
- Dilakukan sampling 5.000 buku unik dari data `Books.csv` untuk content-based filtering agar efisien dan tidak overload RAM.

### 4. Handling Missing Value
- Nilai kosong pada kolom teks seperti `Author` dan `Title` diisi string kosong `''`
- Kolom `Age` yang tidak valid atau kosong dihapus setelah konversi
- Kolom `Title` dan `Author` digabung ke kolom `combined`.
- Nilai kosong diisi dengan string kosong.
- Sampling 5.000 buku unik dilakukan agar tidak kehabisan memori saat TF-IDF.
- Handling Missing Value

---

# Modeling - Content-Based Filtering

Pada pendekatan ini, sistem merekomendasikan buku yang memiliki kemiripan konten berdasarkan judul dan nama penulis.

#### Tahapan:

1. **Gabungkan kolom Title dan Author** ke dalam satu kolom baru bernama `combined`.
2. **Bersihkan data** dengan mengisi nilai kosong.
3. **Lakukan sampling** terhadap 5.000 buku unik agar proses vektorisasi tidak memakan seluruh memori.
4. **Vektorisasi** kolom `combined` menggunakan TF-IDF.
5. **Hitung kemiripan** antar buku dengan *cosine similarity*.
6. **Buat fungsi rekomendasi** berdasarkan input judul dari pengguna.

## Contoh Output – Content-Based Filtering
### Tahap Preparation
- Kolom `Title` dan `Author` digabung menjadi `combined`
- Vektorisasi menggunakan **TF-IDF**
- Kemiripan dihitung menggunakan **cosine similarity**

### Ekstraksi Fitur TF-IDF
- Kolom Title dan Author digabungkan menjadi combined.
- Data diubah menjadi representasi TF-IDF vector menggunakan TfidfVectorizer.
### Hitung Kemiripan
- Kemiripan antar buku dihitung menggunakan cosine similarity antar vektor TF-IDF.

Ketika pengguna memasukkan judul buku **"Lost Laysen"**, sistem merekomendasikan lima buku lain yang memiliki kemiripan dari segi konten, khususnya berdasarkan judul dan nama penulis.
| Judul Buku                            | Penulis              |
|---------------------------------------|-----------------------|
| Firebug                               | Marianne Mitchell     |
| Drawn to the Grave                    | Mary Ann Mitchell     |
| Ghostwritten                          | David Mitchell        |
| Joe Gould's Secret                    | Joseph Mitchell       |
| Lost in My Dreams                     | Faye Ashley           |

### Interpretasi Hasil

- Sebagian besar rekomendasi menampilkan nama belakang **Mitchell**, yang menunjukkan bahwa model memberi bobot tinggi pada kemiripan nama penulis.
- Judul yang mengandung kata kunci terkait perasaan atau misteri (seperti "Dreams", "Grave", "Ghostwritten") menunjukkan bahwa model mengenali pola linguistik tertentu dalam judul.
- Namun, sistem ini **tidak memahami isi cerita secara semantik**, jadi meskipun secara kata terlihat mirip, tema sebenarnya belum tentu sejalan sepenuhnya.

### Catatan
Sistem hanya mengandalkan kemiripan kata dari judul dan penulis. Oleh karena itu, rekomendasi dapat terpengaruh oleh kemiripan nama atau istilah umum, bukan dari pemahaman isi buku.


---


# Modeling – Collaborative Filtering

- Data diformat menjadi `(User-ID, Title, Rating)` menggunakan `Dataset.load_from_df`
- Skala rating diset ke `(0–10)`
- Data dibagi menjadi training 80% dan testing 20% menggunakan `train_test_split`
### Encode Label
.
### Split Data
Data dibagi menjadi train: 80% dan test: 20%.
### Pelatihan Model
Model dilatih menggunakan algoritma SVD dari library surprise.

Untuk pendekatan kedua, digunakan teknik **Collaborative Filtering** berbasis matrix factorization menggunakan algoritma **SVD (Singular Value Decomposition)** dari library `surprise`.

Model ini mempelajari pola interaksi antara pengguna dan item berdasarkan rating historis, tanpa bergantung pada konten buku.

### Tahapan:

1. **Persiapan Data**  
   Menggunakan `User-ID`, `Title`, dan `Rating` sebagai input, lalu dibagi menjadi data pelatihan dan pengujian.

2. **Pelatihan Model**  
   Model dilatih menggunakan algoritma **SVD** dan dievaluasi menggunakan data uji.

3. **Evaluasi Akurasi**  
   Model diuji menggunakan metrik RMSE (Root Mean Square Error) dan menghasilkan nilai: 3.5242

4. **Fungsi Rekomendasi**  
Fungsi `get_recommendations()` digunakan untuk menyarankan buku kepada user berdasarkan estimasi rating tertinggi.

**Contoh Output untuk User-ID: 11676**

| Judul Buku                                          | Estimasi Rating |
|-----------------------------------------------------|------------------|
| A Simple Plan                                       | 10.0             |
| The Hobbit                                          | 10.0             |
| Metaphysique Des Tubes                              | 10.0             |
| The Gunslinger (The Dark Tower, Book 1)             | 10.0             |
| Tears of the Moon (Irish Trilogy)                   | 10.0             |

---

### Model ini cocok untuk:

Pengguna yang telah aktif memberikan rating, karena model ini memanfaatkan pola perilaku antar pengguna lain untuk memprediksi minat.

---

### Perbandingan Pendekatan

| Kriteria             | Content-Based Filtering               | Collaborative Filtering               |
|----------------------|----------------------------------------|----------------------------------------|
| **Basis**            | Fitur buku (judul, penulis)           | Interaksi user–buku (rating)          |
| **Kelebihan**        | Cocok untuk user baru (cold-start)     | Rekomendasi lebih personal            |
| **Kelemahan**        | Tidak mempertimbangkan minat user      | Tidak bisa digunakan untuk user baru  |
| **Rekomendasi**      | Umum berdasarkan konten                | Spesifik ke masing-masing user        |

---

## Evaluasi
- Metrik Evaluasi: RMSE
- Untuk collaborative filtering, digunakan RMSE (Root Mean Square Error) sebagai metrik karena sesuai untuk regresi prediksi rating.
- Hasil evaluasi: ```RMSE: 3.5242```

### Hasil Evaluasi per Pendekatan
| Pendekatan               | Evaluasi / Output                                                                 |
|--------------------------|-----------------------------------------------------------------------------------|
| Content-Based Filtering  | Memberikan rekomendasi buku berbasis kemiripan teks dari judul dan penulis.      |
|                          | Namun hasil bisa tidak akurat jika konten teks tidak cukup representatif.        |
| Collaborative Filtering  | RMSE: **3.5242**                                                                 |
|                          | Memberikan rekomendasi yang lebih personal, cocok untuk pengguna aktif.          |

### Kaitan dengan Business Understanding
| Aspek                        | Penjelasan                                                                                      |
|-----------------------------|-------------------------------------------------------------------------------------------------|
| Problem Statement         | Terjawab. Sistem berhasil memberikan rekomendasi untuk membantu pengguna menemukan buku relevan.|
| Goals                    | Tercapai. Sistem menghasilkan rekomendasi baik berbasis konten maupun preferensi pengguna.     |
| Solusi yang Dirancang     | Bekerja. Pendekatan content-based dan collaborative terbukti mampu menyelesaikan tujuan proyek. |

---

## Insight Akhir

Kombinasi dua pendekatan ini memberikan cakupan lebih luas untuk berbagai tipe pengguna:

- **Content-Based Filtering** bekerja baik untuk pengguna baru.
- **Collaborative Filtering** menghasilkan rekomendasi personal untuk pengguna yang aktif.

Untuk implementasi produksi, pendekatan **hybrid** bisa diterapkan untuk memaksimalkan akurasi dan coverage.
