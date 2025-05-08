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

Dataset terdiri dari:
- **Books.csv** → ISBN, Title, Author, Year, Publisher
- **Users.csv** → User-ID, Age
- **Ratings.csv** → User-ID, ISBN, Rating

### Distribusi Usia Pengguna
Mayoritas pengguna berada dalam rentang usia 20–40 tahun.

### User Paling Aktif
User-ID `11676` memberikan 13,602 rating – kandidat ideal untuk pengujian collaborative filtering.

### Buku Populer
ISBN `0971880107` mendapat 2,502 rating – buku paling sering dinilai.

---

## Data Preparation

- Kolom `Title` dan `Author` digabung ke kolom `combined`.
- Nilai kosong diisi dengan string kosong.
- Sampling 5.000 buku unik dilakukan agar tidak kehabisan memori saat TF-IDF.

# Modeling - Content-Based Filtering

Pada pendekatan ini, sistem merekomendasikan buku yang memiliki kemiripan konten berdasarkan judul dan nama penulis.

#### Tahapan:

1. **Gabungkan kolom Title dan Author** ke dalam satu kolom baru bernama `combined`.
2. **Bersihkan data** dengan mengisi nilai kosong.
3. **Lakukan sampling** terhadap 5.000 buku unik agar proses vektorisasi tidak memakan seluruh memori.
4. **Vektorisasi** kolom `combined` menggunakan TF-IDF.
5. **Hitung kemiripan** antar buku dengan *cosine similarity*.
6. **Buat fungsi rekomendasi** berdasarkan input judul dari pengguna.

### Contoh Output – Content-Based Filtering

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

## Insight Akhir

Kombinasi dua pendekatan ini memberikan cakupan lebih luas untuk berbagai tipe pengguna:

- **Content-Based Filtering** bekerja baik untuk pengguna baru.
- **Collaborative Filtering** menghasilkan rekomendasi personal untuk pengguna yang aktif.

Untuk implementasi produksi, pendekatan **hybrid** bisa diterapkan untuk memaksimalkan akurasi dan coverage.
