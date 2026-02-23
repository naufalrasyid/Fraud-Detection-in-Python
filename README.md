# **💳 Credit Card Fraud Detection: Handling Extreme Class Imbalance**

## **Project Overview**

Proyek ini bertujuan untuk membangun sistem pendeteksi penipuan (*fraud detection system*) berbasis *Machine Learning* untuk transaksi kartu kredit. Tantangan utama dalam proyek ini adalah distribusi kelas yang sangat timpang (*extreme imbalance*), di mana transaksi penipuan hanya mencakup sebagian kecil dari total volume transaksi. Proyek ini membandingkan efektivitas teknik *Oversampling* (SMOTE) melawan *Cost-Sensitive Learning* (Baseline) untuk mengoptimalkan deteksi tanpa mengganggu pengalaman pengguna (*user experience*).

## **Key Features & Methodology**

1. **Feature Engineering:**
* Ekstraksi fitur temporal (`hour`, `day`, `month`) dari *timestamp* untuk mendeteksi anomali perilaku waktu.
* Kalkulasi usia pengguna (`age`) dari tanggal lahir (`dob`).


2. **Data Preprocessing:**
* Penerapan **Target Encoding** untuk fitur kategorikal dengan kardinalitas tinggi (`merchant`, `city`, `job`) agar model menangkap korelasi risiko secara efisien.
* Penerapan **RobustScaler** pada fitur numerik (`amt`, `city_pop`) untuk memitigasi efek *outliers* yang ekstrem pada nominal transaksi.


3. **Modeling Strategy:**
* Mengevaluasi algoritma *tree-based* dan linier (Logistic Regression, Decision Tree, Random Forest, XGBoost).
* Membandingkan data keseimbangan buatan (SMOTE) dengan bobot skala asimetris pada data asli (Baseline).


4. **Evaluation Metrics:**
* Fokus utama pada **AUPRC (Area Under the Precision-Recall Curve)**, yang memberikan representasi performa paling akurat untuk dataset *imbalanced*, dibandingkan dengan ROC-AUC.



## **Key Insights & Results**

* **Threshold Risiko:** Risiko *fraud* melonjak eksponensial saat nominal transaksi melampaui **$135**.
* **Anomali Temporal:** Tingkat penipuan tertinggi terjadi pada jam pengawasan manusia terendah, yakni antara pukul **22:00 hingga 03:00 dini hari**.
* **Model Pemenang:** **XGBoost Baseline (Cost-Sensitive)** adalah model terbaik dengan skor **AUPRC 0.8915**. Model ini mengungguli pendekatan SMOTE dengan berhasil menangkap lebih banyak kasus penipuan (*High Recall*) sekaligus mempertahankan presisi tinggi, mencegah alarm palsu yang berlebihan pada nasabah sah.
* **Feature Importance:** Fitur geografis (`city`) dan nominal transaksi (`amt`) adalah prediktor paling krusial dalam membedakan transaksi legal dan ilegal.

---

### **Pertanyaan Bisnis yang Terjawab oleh Proyek Ini**

**1. Bagaimana perusahaan dapat menekan kerugian finansial akibat penipuan kartu kredit tanpa membuat nasabah sah merasa terganggu oleh pemblokiran akun yang tidak perlu?**

* **Jawaban Analitis:** Dengan menerapkan model **XGBoost Baseline** yang mengadopsi prinsip *Cost-Sensitive Learning*.
* **Dampak Bisnis:** Model ini dirancang untuk mencapai *sweet spot* antara Presisi dan Recall (AUPRC 0.89). Secara operasional, sistem akan berhasil memblokir mayoritas ancaman (374 dari 429 kasus penipuan dalam uji coba) dengan tingkat *False Positive* yang dapat ditoleransi. Hal ini menyelamatkan miliaran rupiah dari potensi kerugian (*prevented loss*) tanpa perlu membebani tim *Customer Service* dengan keluhan nasabah yang transaksinya ditolak secara keliru.

**2. Kriteria transaksi seperti apa yang secara otomatis harus memicu sistem keamanan (seperti verifikasi OTP/Biometrik) untuk memitigasi risiko secara *real-time*?**

* **Jawaban Analitis:** Berdasarkan analisis *Feature Importance* dan EDA, sistem keamanan (*Rule-based engine* pendamping ML) harus otomatis meminta verifikasi tambahan jika sebuah transaksi memenuhi kombinasi profil risiko tinggi berikut:
* Nominal transaksi (**`amt`**) berada di atas nilai kritis **$135**.
* Transaksi dilakukan pada rentang waktu (**`hour`**) anomali, yakni pukul **22:00 hingga 03:00**.
* Terjadi perubahan atau ketidakwajaran pada lokasi (**`city`**) yang dieksekusi melalui *merchant* tertentu.


* **Dampak Bisnis:** Penambahan otentikasi dinamis (*Dynamic Friction*) ini memberikan lapisan pertahanan proaktif (mencegah *fraud* sebelum dana berpindah), namun hanya diaktifkan pada sebagian kecil transaksi sehingga mayoritas nasabah tetap menikmati pengalaman bertransaksi yang mulus.
