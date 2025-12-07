
 ## Prediksi Prioritas Perbaikan Sarpras SD di Indonesia Menggunakan Random Forest

## GAMBARAN PROYEK :
Proyek ini bertujuan untuk mengolah dan menggabungkan beberapa indikator sarana-prasarana (sarpras) Sekolah Dasar (SD) di Indonesia pada level provinsi, lalu memprediksi **tingkat prioritas perbaikan sarpras** ke dalam 3 kategori: **Prioritas Rendah, Prioritas Sedang, dan Prioritas Tinggi**.  
Data yang digunakan berasal dari portal publik dan terdiri dari indikator **kondisi ruang kelas**, **akses listrik**, serta **ketersediaan fasilitas komputer untuk pengajaran**.  
Solusi akhir proyek ini berupa **aplikasi dashboard web sederhana berbasis Gradio** yang memungkinkan pengguna memilih provinsi dan melihat hasil prediksi prioritas beserta probabilitasnya.

---

## METODOLOGI (CRISP-DM):
Proyek ini mengikuti 6 tahapan standar sebagai berikut:

### 1) Business Understanding (Pemahaman Bisnis)
Masalah utama adalah **ketimpangan kondisi sarpras SD** antar provinsi dan belum adanya pemetaan prioritas yang menggabungkan beberapa indikator sekaligus.  
Tujuannya adalah menghasilkan rekomendasi awal berbasis data untuk menentukan **provinsi mana yang lebih perlu diprioritaskan** dalam program perbaikan sarpras.

### 2) Data Understanding (Pemahaman Data)
Dataset yang digunakan adalah 3 dataset sarpras SD (format Excel) dari portal publik:
1. **Proporsi sekolah SD dengan akses ke listrik**  
2. **Jumlah SD yang memiliki fasilitas komputer untuk tujuan pengajaran (2024)**  
3. **Jumlah ruang kelas SD menurut kondisi tiap provinsi (2023)**  

Variabel kunci meliputi:
- **PROVINSI**
- Indikator numerik sarpras (jumlah/proporsi/persen) terkait listrik, komputer, dan kondisi ruang kelas.

### 3) Data Preparation (Persiapan Data)
Tahapan persiapan data meliputi:
- Menentukan baris header yang benar (karena file portal kadang header tidak di baris pertama).
- Standarisasi nama provinsi (misal menghapus “PROV.”/“PROVINSI”, menyesuaikan penulisan beberapa provinsi) agar data bisa digabung.
- Konversi data numerik dari teks (hapus `%`, ubah koma menjadi titik, menghapus karakter non angka).
- Menghapus baris non-provinsi seperti “TOTAL/INDONESIA/LUAR NEGERI” jika ada.
- Menggabungkan 3 dataset berdasarkan kolom **PROVINSI**.
- Menangani missing value:
  - Kolom yang 100% kosong dibuang
  - Sisanya diisi median agar tidak error dan tidak menghasilkan NaN.

**Feature Engineering (pembuatan label):**  
Karena data portal tidak menyediakan label “prioritas perbaikan”, dibuat **skor prioritas** dari gabungan fitur sarpras:
- Indikator kerusakan/risiko (misal ruang rusak) menaikkan skor
- Indikator kondisi baik menurunkan skor  
Skor dibagi menjadi 3 kelas menggunakan batas kuantil 33% dan 66% → label prioritas (*Rendah/Sedang/Tinggi*).

### 4) Modeling (Pemodelan)
Algoritma yang digunakan adalah **Random Forest Classifier (Supervised Learning)**.  
Fitur model adalah seluruh indikator numerik hasil merge. Target model adalah kelas prioritas perbaikan sarpras.

### 5) Evaluation (Evaluasi)
Evaluasi dilakukan menggunakan:
- **Train-test split stratified**
- **Accuracy** dan **Macro-F1**
- **Confusion Matrix**

### 6) Deployment (Penerapan)
Deployment dilakukan menggunakan **Gradio** dengan konsep aplikasi sederhana:
- Input: dropdown provinsi  
- Output: prediksi prioritas (Rendah/Sedang/Tinggi) dan probabilitas tiap kelas  
Model dan dataset final dibaca dari folder `output/`.

---

## TEKNOLOGI YANG DIGUNAKAN :
- Bahasa Pemrograman: **Python**
- Pengolahan data: **Pandas, NumPy**
- Machine Learning: **Scikit-Learn**
- Visualisasi: **Matplotlib**
- Deployment UI: **Gradio**
- Penyimpanan model: **Joblib**

---

 CARA MENJALANKAN PROJECT:

### Langkah 1: Instalasi Library
Pastikan Python sudah terinstal, lalu jalankan:
```bash
pip install pandas numpy scikit-learn matplotlib joblib gradio
```

### Langkah 2: Persiapan Dataset
Pastikan 3 file Excel berikut berada di folder yang sama dengan notebook training:
- `proporsi-sekolah-dengan-akses-ke-listrik-pada-jenjang-sd.xlsx`
- `jumlah-sd-yang-memiliki-fasilitas-komputer-untuk-tujuan-pengajaran-2024.xlsx`
- `jumlah-ruang-kelas-menurut-kondisi-tiap-provinsi-sd-2023.xlsx`

### Langkah 3: Jalankan Training (Notebook)
Buka dan jalankan notebook training sampai selesai agar menghasilkan file output:
- `output/sarpras_sd_prioritas_processed.csv`
- `output/rf_prioritas_sarpras_sd.joblib`

### Langkah 4: Jalankan Dashboard Gradio
Buka notebook Gradio lalu **Run All Cells**, akses link yang muncul.
Default lokal:
- `http://127.0.0.1:7860`

## STRUKTUR DATA:

### Data Mentah (Input)
Data awal berupa 3 file Excel terpisah yang masing-masing memuat indikator sarpras SD per provinsi (listrik, komputer, kondisi ruang kelas).

### Data Terproses (Output)
Data setelah diproses menjadi 1 tabel gabungan level provinsi:
- **PROVINSI**
- Fitur numerik gabungan (RUANG_*, LIS_*, KOM_*)
- **PRIORITY_SCORE**
- **PRIORITAS_PERBAIKAN_SARPRAS** (label 3 kelas)

---

## INTERPRETASI HASIL MODEL:
Model Random Forest akan memprediksi provinsi ke dalam 3 kategori:
- **Prioritas Tinggi:** kondisi sarpras relatif lebih membutuhkan (misal ruang rusak lebih besar / indikator pendukung rendah)
- **Prioritas Sedang:** kondisi menengah
- **Prioritas Rendah:** kondisi relatif lebih baik dibanding provinsi lain (berdasarkan skor gabungan)

Catatan: hasil ini adalah **rekomendasi awal berbasis data**, bukan keputusan final.

---

## KONTRIBUSI :
Proyek ini disusun untuk memenuhi tugas Mata Kuliah **APPLIED MACHINE LEARNING**.

**Nama:** Fauzan Muhammad Zahran

**Kelas:** 5AI-A
