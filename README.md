# Perbandingan Metode Modified Z-Score dan Isolation Forest dalam Deteksi Outlier pada Data Time-Series Ketinggian Gelombang Laut

Repositori ini berisi proyek kelompok untuk mata kuliah **Penambangan Data (IF25-32025)** di **Institut Teknologi Sumatera (ITERA)**. Proyek ini berfokus pada tahap *preprocessing* data, khususnya perbandingan metode statistik *robust* dan *machine learning* dalam mendeteksi serta menangani *outlier* pada data deret waktu (*time-series*) oseanografi.

---

## 📌 Anggota Tim (Kelompok 6)
Semua anggota berkontribusi setara (masing-masing **14.3%**) dalam penyusunan proyek ini:
* **Ade Putri Tifani** (123140011)
* **Natasya Felisita Br Ginting** (123140017)
* **Memory Simanjuntak** (123140095)
* **Silvia** (123140133)
* **Nadya Shafwah Yusuf** (123140167)
* **Mega Zayyani** (123140180)
* **Yuni Okta Safitri** (123140213)

**Dosen Pengampu:** Meida Cahyo Untoro, S.Kom., M.Kom

---

## 📖 Latar Belakang & Masalah
Data gelombang laut yang diperoleh dari sensor satelit *Synthetic Aperture Radar* (SAR) kerap mengandung *noise* dan ketidakkonsistenan nilai (pencilan) akibat faktor lingkungan yang dinamis. Jika langsung digunakan untuk pemodelan, *outlier* ini dapat menurunkan akurasi model klasifikasi hilir (*downstream model*). 

Proyek ini mengevaluasi dua pendekatan deteksi *outlier*:
1.  **Modified Z-Score:** Pendekatan statistik *robust* yang memanfaatkan Median dan *Median Absolute Deviation* (MAD).
2.  **Isolation Forest:** Pendekatan *machine learning* berbasis pohon keputusan (*decision tree*) untuk mengisolasi anomali.

---

## 📊 Karakteristik Dataset
Dataset yang digunakan diambil dari **Australian Ocean Surface Waves Dataset from SAR (Level-2P)** melalui *Australian Ocean Data Network* (AODN) dengan spesifikasi:
* **Rentang Waktu:** Januari s.d. April 2022 (setelah *resampling* per 3 jam).
* **Ukuran Data:** 697 observasi (557 sampel *training*, 140 sampel *testing* menggunakan *sequential time-series split* 80:20).
* **Fitur Numerik (7):** `EKTH`, `DIRECTION`, `HS_PART` (Tinggi gelombang signifikan), `WP_PART`, `WSPD_ECMWF`, `WDIR_ECMWF`, `BOT_DEPTH`, `DIST2COAST`.
* **Variabel Target:** `Outlier_Label` (0 = Inlier ~90%, 1 = Outlier ~10% berdasarkan kuantil ke-90 dari `HS_PART`).

---

## ⚙️ Alur Penelitian & Metodologi
Proyek ini diimplementasikan menggunakan arsitektur eksperimen yang ketat yaitu:
1.  **Exploratory Data Analysis (EDA):** Visualisasi sebaran fitur numerik menggunakan Boxplot dan Histogram.
2.  **Data Splitting:** Pembagian data secara berurutan (*sequential split*) untuk menghindari *data leakage* pada data deret waktu.
3.  **Outlier Treatment:** Mengidentifikasi pencilan pada data *training* dan melakukan penanganan dengan strategi **Removal** (penghapusan total baris anomali).
    * *Threshold Modified Z-Score:* $|M_i| > 3.5$
    * *Contamination Isolation Forest:* $0.05$
4.  **Downstream Classification:** Melatih model **Random Forest Classifier** ($n\_estimators=100$, $random\_state=42$) pada data yang telah dibersihkan.
5.  **Evaluasi Objektif:** Model diuji menggunakan **test set asli** yang tidak disentuh oleh modifikasi *outlier* apapun menggunakan metrik *macro-average* ($F1\text{-}Score$, $Precision$, $Recall$, $AUC\text{-}ROC$, dan $Confusion\ Matrix$).

---

## 📈 Ringkasan Hasil Eksperimen

### Jumlah Outlier Terdeteksi (Data Training)
* **Modified Z-Score:** 27 sampel (4.85%)
* **Isolation Forest:** 28 sampel (5.03%)

### Performa Klasifikasi pada Test Set Asli
| Skenario / Strategi Treatment | Accuracy | Precision (macro) | Recall (macro) | F1-Score (macro) | AUC-ROC |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Baseline (Tanpa Treatment)** | 0.9214 | 0.7841 | 0.7659 | 0.7746 | 0.9535 |
| **Removal - Modified Z-Score** | **0.9286** | **0.8099** | **0.7698** | **0.7880** | **0.9657** |
| **Removal - Isolation Forest** | 0.9214 | 0.7841 | 0.7659 | 0.7746 | 0.9646 |

### 💡 Kesimpulan Utama
* **Modified Z-Score menghasilkan performa terbaik** dengan kenaikan F1-Score sebesar **1.73%** dibanding baseline. Metode ini berhasil menekan angka *False Positive* pada model klasifikasi (mengurangi kesalahan deteksi data normal menjadi anomali).
* **Isolation Forest tidak menunjukkan perubahan performa** pada metrik F1-Score karena sifatnya yang multivariat cenderung mengisolasi data ekstrem yang valid (*legitimate extreme*), sehingga menghapus informasi penting yang dibutuhkan oleh model klasifikasi.

---

## 🛠️ Tech Stack & Library
* **Language:** Python
* **Environment:** Google Colab
* **Libraries:** `scikit-learn`, `pandas`, `numpy`, `matplotlib`, `seaborn`
