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

```python
data['combined'] = data['Title'] + ' ' + data['Author']
data['combined'] = data['combined'].fillna('')
