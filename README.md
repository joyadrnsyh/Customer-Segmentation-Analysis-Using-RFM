<img width="1536" height="1024" alt="ChatGPT Image May 16, 2026, 04_24_44 PM" src="https://github.com/user-attachments/assets/e9f19223-2e64-4260-a5d0-230ae2787ce6" />


# Laporan Analisis Customer Segmentation — RFM
**Dataset:** Raw RFM Sales Transactions 2025 | **Dibuat:** Januari 2026

---

## Daftar Isi
1. [Ringkasan Eksekutif](#1-ringkasan-eksekutif)
2. [Dataset & Preprocessing](#2-dataset--preprocessing)
3. [Exploratory Data Analysis](#3-exploratory-data-analysis)
4. [Kalkulasi & Skoring RFM](#4-kalkulasi--skoring-rfm)
5. [Segmentasi Customer](#5-segmentasi-customer)
6. [Analisis per Region & Kategori](#6-analisis-per-region--kategori)
7. [Business Insights & Rekomendasi](#7-business-insights--rekomendasi)
8. [Kesimpulan](#8-kesimpulan)

---

## 1. Ringkasan Eksekutif

Analisis ini menerapkan metode **RFM (Recency, Frequency, Monetary)** terhadap 2.000 transaksi penjualan sepanjang tahun 2025 untuk mengklasifikasikan 812 customer unik ke dalam 11 segmen perilaku yang dapat ditindaklanjuti secara strategis.

### Temuan Utama

| Indikator | Nilai |
|-----------|-------|
| Total Transaksi | 2.000 |
| Customer Unik | 812 |
| Produk Unik | 10 |
| Total Revenue | Rp 59,85 Miliar |
| Rata-rata Nilai Order | Rp 29,93 Juta |
| Periode Data | 1 Jan 2025 – 31 Des 2025 |
| Snapshot Date RFM | 1 Januari 2026 |

### Sorotan Kritis

- **Champions (17,1% customer)** menyumbang **63,2% dari total revenue** — menjadikan mereka aset terpenting yang wajib dijaga.
- **Loyal Customers (18,5%)** berkontribusi **18,7% revenue** — kelompok terbesar dan paling stabil.
- **Lost (19,3% customer)** hanya menyumbang **3,4% revenue** — perlu investigasi penyebab churn.
- **At Risk (8,9%)** memiliki rata-rata monetary tinggi (Rp 70,2 Juta/customer) namun mulai tidak aktif — intervensi segera diperlukan.

---

## 2. Dataset & Preprocessing

### 2.1 Struktur Data

Dataset memiliki 10 kolom dengan 2.000 baris transaksi:

| Kolom | Tipe Asal | Tipe Setelah Parsing | Keterangan |
|-------|-----------|----------------------|------------|
| Transaction ID | string | string | ID unik transaksi |
| Date | string | datetime | Format `dd-mm-yyyy` |
| Product ID | string | string | Kode produk |
| Product Name | string | string | Nama produk |
| Product Category | string | string | 3 kategori |
| Quantity | integer | integer | Jumlah unit |
| PPU | string | float | Harga per unit (ada koma) |
| Amount | string | float | Total nilai transaksi (ada koma) |
| Customer ID | string | string | ID customer |
| Region | string | string | 5 region Myanmar |

### 2.2 Hasil Pemeriksaan Kualitas Data

| Pemeriksaan | Hasil |
|-------------|-------|
| Missing values | **0** — tidak ada data hilang |
| Baris duplikat penuh | **0** |
| Transaction ID duplikat | **0** |
| Amount ≤ 0 | **0** — semua transaksi valid |
| Quantity ≤ 0 | **0** — semua kuantitas valid |
| Konsistensi Amount vs Qty × PPU | **Konsisten** (selisih < 1) |

### 2.3 Outlier pada Nilai Transaksi

Deteksi outlier menggunakan metode IQR:

| Statistik | Nilai |
|-----------|-------|
| Q1 (Amount) | Rp 3,74 Juta |
| Q3 (Amount) | Rp 17,55 Juta |
| IQR | Rp 13,81 Juta |
| Jumlah Outlier (> Q3 + 1,5×IQR) | **272 transaksi (13,6%)** |

> **Catatan:** Outlier tidak dihapus karena mencerminkan transaksi korporat/bulk yang sah, bukan kesalahan data. Keberadaannya justru relevan untuk segmen Champions dan Loyal Customers.

---

## 3. Exploratory Data Analysis

### 3.1 Tren Revenue Bulanan

| Bulan | Revenue (Juta) | Transaksi | Customer Aktif |
|-------|---------------|-----------|----------------|
| Jan 2025 | 4.675,0 | 187 | 167 |
| Feb 2025 | 4.572,8 | 124 | 115 |
| Mar 2025 | 3.921,7 | 165 | 151 |
| Apr 2025 | 4.878,6 | 198 | 173 |
| Mei 2025 | 4.192,8 | 150 | 138 |
| Jun 2025 | 4.739,5 | 143 | 128 |
| Jul 2025 | 4.464,5 | 154 | 141 |
| **Agu 2025** | **7.101,8** | 145 | 135 |
| Sep 2025 | 5.252,9 | 200 | 176 |
| Okt 2025 | 4.605,2 | 182 | 168 |
| **Nov 2025** | **7.064,1** | 162 | 138 |
| Des 2025 | 4.382,6 | 190 | 163 |

**Observasi:**
- Revenue tertinggi terjadi di **Agustus (Rp 7,10 Miliar)** dan **November (Rp 7,06 Miliar)** — kemungkinan didorong pembelian bulk korporat berskala besar.
- Revenue terendah di **Maret (Rp 3,92 Miliar)** — peluang untuk kampanye mid-year stimulus.
- Jumlah transaksi dan customer aktif relatif stabil di kisaran 130–200 per bulan, mengindikasikan base customer yang konsisten.

### 3.2 Revenue per Region

| Region | Revenue (Juta) | Persentase |
|--------|---------------|------------|
| **Yangon** | 22.984,4 | **38,4%** |
| Mandalay | 16.437,7 | 27,5% |
| Bago | 9.236,3 | 15,4% |
| Naypyitaw | 7.451,9 | 12,5% |
| Taunggyi | 3.741,3 | 6,3% |

Yangon mendominasi dengan 38,4% revenue, diikuti Mandalay. Taunggyi adalah region dengan kontribusi terendah dan berpotensi untuk ekspansi pasar.

### 3.3 Revenue per Kategori Produk

| Kategori | Revenue (Juta) | Persentase |
|----------|---------------|------------|
| **Electronics** | 43.207,7 | **72,2%** |
| Furniture | 15.144,1 | 25,3% |
| Office Supplies | 1.499,8 | 2,5% |

Electronics mendominasi secara signifikan dengan 72,2% revenue. Office Supplies hanya 2,5% — meskipun mungkin berfungsi sebagai entry-point untuk customer baru.

### 3.4 Top 10 Produk berdasarkan Revenue

| Peringkat | Produk | Revenue (Juta) | % dari Total |
|-----------|--------|---------------|--------------|
| 1 | 27-inch 4K Monitor | 19.999,2 | 33,4% |
| 2 | Noise-Cancelling Headphones | 14.587,2 | 24,4% |
| 3 | Standing Desk Converter | 7.989,0 | 13,3% |
| 4 | Ergonomic Mesh Chair | 5.343,8 | 8,9% |
| 5 | Wireless Mechanical Keyboard | 4.235,0 | 7,1% |
| 6 | USB-C Docking Station | 2.649,2 | 4,4% |
| 7 | Adjustable Monitor Arm | 1.811,3 | 3,0% |
| 8 | Webcam 1080p HD | 1.737,0 | 2,9% |
| 9 | Executive Ballpoint Pen Box | 1.183,6 | 2,0% |
| 10 | A4 Printer Paper (500 Sheets) | 316,2 | 0,5% |

> **27-inch 4K Monitor** dan **Noise-Cancelling Headphones** bersama menyumbang **57,8% dari total revenue** — dua produk ini adalah tulang punggung bisnis dan harus mendapat prioritas dalam supply chain dan strategi promosi.

### 3.5 Distribusi Perilaku Customer

| Metrik | Min | Median | Rata-rata | Max |
|--------|-----|--------|-----------|-----|
| Jumlah Order per Customer | 1 | 2 | 2,46 | 10 |
| Total Spend per Customer (Juta) | — | 18,35 | 73,71 | 1.516,25 |

- Mayoritas customer melakukan **1–3 transaksi** sepanjang tahun — menunjukkan masih besarnya peluang peningkatan frekuensi pembelian.
- Distribusi spend sangat **right-skewed**: sebagian kecil customer high-value mengangkat rata-rata jauh di atas median.

---

## 4. Kalkulasi & Skoring RFM

### 4.1 Definisi Metrik

| Metrik | Formula | Interpretasi |
|--------|---------|--------------|
| **Recency (R)** | Hari dari transaksi terakhir ke snapshot date (1 Jan 2026) | Makin kecil = makin baik |
| **Frequency (F)** | Jumlah transaksi unik per customer | Makin besar = makin baik |
| **Monetary (M)** | Total nilai belanja (dalam Juta) | Makin besar = makin baik |

### 4.2 Statistik RFM

| Metrik | Min | Median | Rata-rata | Max |
|--------|-----|--------|-----------|-----|
| Recency (hari) | 1 | 94 | 122,3 | 365 |
| Frequency | 1 | 2 | 2,46 | 10 |
| Monetary (Juta) | — | 18,35 | 73,71 | 1.516,25 |

### 4.3 Metode Skoring Kuintil (1–5)

Setiap dimensi dibagi ke **5 kuintil** dan diberi skor 1–5:

- **Recency:** Dibalik — recency kecil (baru bertransaksi) mendapat **skor 5**.
- **Frequency & Monetary:** Normal — nilai besar mendapat **skor 5**.

```
RFM Code = R_score (string) + F_score (string) + M_score (string)
Contoh: "555" = Champions tertinggi | "111" = Lost terparah
```

---

## 5. Segmentasi Customer

### 5.1 Aturan Segmentasi

Segmen ditentukan berdasarkan kombinasi skor R, F, M:

| Segmen | Kriteria Skor |
|--------|--------------|
| Champions | R ≥ 4, F ≥ 4, M ≥ 4 |
| Loyal Customers | R ≥ 3, F ≥ 3, M ≥ 3 |
| Potential Loyalist | R ≥ 3, F ≥ 2, M ≥ 2 |
| New Customers | R ≥ 4, F ≤ 2 |
| Promising | R ≥ 3, F ≤ 2, M ≤ 2 |
| At Risk | R = 2, F ≥ 3, M ≥ 3 |
| Cant Lose Them | R ≤ 2, F ≥ 4, M ≥ 4 |
| Need Attention | R ≤ 2, F ≥ 3, M ≤ 2 |
| About To Sleep | R = 2, F ≤ 2, M ≤ 2 |
| Hibernating | R = 1, F ≥ 3 |
| Lost | Tidak masuk kriteria di atas |

### 5.2 Profil Lengkap per Segmen

| Segmen | Customer | % Cust | Avg Recency (hari) | Avg Freq | Avg Monetary (Juta) | Total Rev (Juta) | % Revenue |
|--------|----------|--------|-------------------|----------|---------------------|-----------------|-----------|
| 🏆 Champions | 139 | 17,1% | 31,1 | 4,7 | 272,1 | 37.815,7 | **63,2%** |
| 💎 Loyal Customers | 150 | 18,5% | 63,1 | 3,1 | 74,8 | 11.218,4 | **18,7%** |
| ⚠️ At Risk | 72 | 8,9% | 160,8 | 3,2 | 70,2 | 5.055,9 | 8,4% |
| 💀 Lost | 157 | 19,3% | 248,6 | 1,2 | 13,0 | 2.035,0 | 3,4% |
| 🆕 New Customers | 78 | 9,6% | 39,1 | 1,2 | 15,5 | 1.206,6 | 2,0% |
| 📈 Potential Loyalist | 68 | 8,4% | 74,2 | 2,1 | 11,7 | 797,7 | 1,3% |
| ❄️ Hibernating | 25 | 3,1% | 255,5 | 2,2 | 28,1 | 703,5 | 1,2% |
| 😴 About To Sleep | 61 | 7,5% | 176,5 | 1,1 | 5,5 | 335,0 | 0,6% |
| 🚨 Cant Lose Them | 6 | 0,7% | 256,5 | 3,3 | 46,4 | 278,6 | 0,5% |
| 📣 Need Attention | 28 | 3,4% | 192,5 | 2,1 | 8,5 | 239,3 | 0,4% |
| ✨ Promising | 28 | 3,4% | 96,6 | 1,0 | 5,9 | 165,8 | 0,3% |
| **Total** | **812** | **100%** | — | — | — | **59.851,5** | **100%** |

### 5.3 Analisis per Segmen

#### 🏆 Champions (139 customer | 63,2% revenue)
Customer terbaik. Bertransaksi sangat baru (rata-rata 31 hari lalu), sangat sering (4,7x), dan menghabiskan rata-rata **Rp 272,1 Juta**. Kelompok ini adalah sumber utama revenue dan merupakan kandidat terkuat untuk program referral, VIP event, dan early access produk baru.

#### 💎 Loyal Customers (150 customer | 18,7% revenue)
Kelompok terbesar kedua dalam hal jumlah customer. Recency moderat (63 hari) dengan frekuensi cukup tinggi. Mereka adalah fondasi bisnis yang stabil dan kandidat ideal untuk di-upgrade menjadi Champions melalui program loyalitas berjenjang.

#### ⚠️ At Risk (72 customer | 8,4% revenue)
Pernah aktif dan bernilai tinggi (avg monetary Rp 70,2 Juta) namun kini tidak bertransaksi selama rata-rata **160 hari**. Mereka adalah **prioritas intervensi segera** — setiap customer yang berhasil di-retain setara dengan puluhan customer baru.

#### 🆕 New Customers (78 customer | 2,0% revenue)
Baru bergabung (avg 39 hari) namun frekuensi masih rendah (1,2x). Potensi besar untuk dikembangkan. Pengalaman onboarding yang baik di fase ini menentukan apakah mereka akan menjadi Loyal atau langsung Lost.

#### 💀 Lost (157 customer | 3,4% revenue)
Kelompok terbesar (19,3% customer) namun kontribusi revenue sangat kecil. Rata-rata tidak bertransaksi selama **248 hari**. Re-aktivasi memerlukan biaya tinggi dengan probabilitas rendah — lebih efisien mengalokasikan budget ke segmen yang lebih promising.

#### 🚨 Cant Lose Them (6 customer | 0,5% revenue)
Meski jumlahnya sangat kecil, mereka pernah menjadi customer sangat aktif (F ≥ 4, M ≥ 4) namun kini hilang (R ≤ 2). Kontak personal dan penawaran eksklusif diperlukan segera.

---

## 6. Analisis per Region & Kategori

### 6.1 Sebaran Segmen per Region

| Region | Champions | Loyal | At Risk | Lost | New | Potential L. | Others |
|--------|-----------|-------|---------|------|-----|--------------|--------|
| Yangon | 51 | 40 | 15 | 31 | 15 | 20 | 36 |
| Mandalay | 50 | 39 | 18 | 34 | 15 | 14 | 28 |
| Bago | 12 | 23 | 13 | 34 | 18 | 8 | 23 |
| Naypyitaw | 15 | 25 | 15 | 31 | 16 | 16 | 33 |
| Taunggyi | 11 | 23 | 11 | 27 | 14 | 10 | 28 |

**Observasi:**
- **Yangon & Mandalay** memiliki Champions terbanyak (51 dan 50) — market premium terkuat ada di dua kota utama ini.
- **Bago** memiliki proporsi Lost yang cukup tinggi relatif terhadap Champions (34 vs 12) — membutuhkan strategi akuisisi dan retensi yang lebih agresif.
- **Taunggyi** adalah region dengan customer paling sedikit di hampir semua segmen — potensi ekspansi belum tergarap optimal.

### 6.2 Sebaran Segmen per Kategori Produk Favorit

| Kategori | Champions | Loyal | At Risk | Lost | New | Potential L. |
|----------|-----------|-------|---------|------|-----|--------------|
| **Electronics** | **104** | **110** | **54** | **78** | **40** | **43** |
| Furniture | 33 | 30 | 12 | 56 | 22 | 19 |
| Office Supplies | 2 | 10 | 6 | 23 | 16 | 6 |

**Observasi:**
- Customer dengan produk favorit **Electronics** mendominasi hampir semua segmen positif (Champions, Loyal, Potential Loyalist).
- **Furniture** memiliki proporsi Lost yang relatif tinggi (56 customer) dibanding Champions (33) — segmen ini perlu strategi retensi khusus.
- **Office Supplies** banyak ditemukan di segmen Lost dan New Customers — produk ini cenderung menjadi pembelian satu kali atau entry-point tanpa konversi ke pembelian berikutnya.

---

## 7. Business Insights & Rekomendasi

### 7.1 Matriks Prioritas Aksi

| Prioritas | Segmen | Customer | Revenue Potensial | Aksi Utama |
|-----------|--------|----------|------------------|------------|
| 🔴 Kritis | At Risk | 72 | Rp 5,06 Miliar terancam | Re-engagement campaign personal |
| 🔴 Kritis | Cant Lose Them | 6 | High-value hilang | Hubungi langsung, win-back eksklusif |
| 🟠 Tinggi | Champions | 139 | 63% revenue — jaga ketat | VIP program, reward, referral |
| 🟠 Tinggi | Loyal Customers | 150 | Upgrade ke Champions | Loyalty tier, upsell premium |
| 🟡 Sedang | Potential Loyalist | 68 | Konversi ke Loyal | Email series, diskon 2nd purchase |
| 🟡 Sedang | New Customers | 78 | Akuisisi baru | Welcome flow, edukasi produk |
| 🟢 Rendah | Lost | 157 | ROI rendah | Survei exit, alokasi budget minimal |

### 7.2 Rekomendasi Spesifik per Segmen

#### 🏆 Champions
- Buat **program VIP eksklusif** dengan benefit nyata: early access produk baru, layanan prioritas, gratis ongkir unlimited.
- Aktifkan **program referral** — Champions adalah brand ambassador organik terbaik.
- Berikan **reward kejutan** (surprise & delight) untuk mempertahankan engagement emosional.
- Target: pertahankan ≥90% dari 139 Champions untuk tahun berikutnya.

#### 💎 Loyal Customers
- Implementasikan **loyalty points** yang dapat ditukar produk atau diskon.
- Lakukan **upsell** ke produk Electronics premium (4K Monitor, Headphones).
- Tawarkan **bundle produk** yang meningkatkan nilai transaksi per kunjungan.
- Target: konversi minimal 30% (45 customer) menjadi Champions dalam 6 bulan.

#### ⚠️ At Risk
- Kirimkan **email re-engagement personal** dengan subject line yang menyebut nama customer.
- Tawarkan **diskon eksklusif 15–20%** dengan batas waktu 14 hari.
- Lakukan **survei kepuasan singkat** untuk memahami penyebab penurunan aktivitas.
- Target: re-aktivasi minimal 40% (29 customer) dalam 30 hari pertama kampanye.

#### 🆕 New Customers
- Kirimkan **welcome email series** (3–5 email dalam 30 hari pertama).
- Berikan **voucher pembelian kedua** yang berlaku 30 hari setelah transaksi pertama.
- Tampilkan **rekomendasi produk personal** berdasarkan kategori pembelian pertama.
- Target: konversi 50% menjadi repeat buyer dalam 60 hari.

#### 📈 Potential Loyalist
- Fokus pada **peningkatan frekuensi**: dorong minimal satu transaksi tambahan melalui campaign seasonal.
- Tunjukkan **program loyalitas** yang akan mereka dapatkan jika naik ke Loyal Customers.

#### 😴 About To Sleep + 📣 Need Attention
- Gabungkan dalam satu **re-activation blast campaign** dengan penawaran terbatas.
- Gunakan **push notification** dan SMS jika ada kontak.
- Jika tidak merespons dalam 60 hari, pindahkan ke bucket "hibernasi pasif".

#### 💀 Lost
- Kirimkan satu kali **survei keluar** untuk memahami penyebab churn — data ini lebih berharga daripada mencoba mengaktifkan ulang.
- Alokasikan **<5% budget retensi** untuk segmen ini.
- Realokasikan sumber daya ke akuisisi customer baru dan penguatan segmen aktif.

### 7.3 Rekomendasi Strategis per Region

| Region | Prioritas | Strategi |
|--------|-----------|----------|
| **Yangon** | Pertahankan Champions | Program VIP, event eksklusif |
| **Mandalay** | Upgrade Loyal → Champions | Loyalty tier acceleration |
| **Bago** | Kurangi Lost, tingkatkan retention | Re-engagement + akuisisi baru |
| **Naypyitaw** | Konversi Potential Loyalist | Email nurturing, seasonal promo |
| **Taunggyi** | Ekspansi pasar | Awareness campaign, partnership lokal |

### 7.4 Rekomendasi Strategis per Kategori

| Kategori | Insight | Aksi |
|----------|---------|------|
| **Electronics** | Dominan — 72% revenue, customer bernilai tinggi | Prioritaskan stok, perpanjang garansi, cross-sell aksesoris |
| **Furniture** | Lost tinggi — kemungkinan pembelian satu kali | Tambahkan layanan pasca jual, instalasi, maintenance |
| **Office Supplies** | Entry-point, konversi rendah | Bundle dengan Electronics, jadikan trigger frekuensi |

---

## 8. Kesimpulan

### Ringkasan Temuan

Analisis RFM mengungkap struktur customer yang sangat **terkonsentrasi**: hanya **35,6% customer** (Champions + Loyal Customers = 289 orang) menghasilkan **81,9% dari total revenue Rp 59,85 Miliar**. Ini adalah pola Pareto klasik yang mengharuskan bisnis untuk sangat selektif dalam mengalokasikan sumber daya retensi.

```
Revenue Concentration:
  Top 17% customer (Champions)     → 63.2% revenue
  Next 18% customer (Loyal)        → 18.7% revenue
  ─────────────────────────────────────────────────
  35% customer teratas             → 81.9% revenue
  65% customer sisanya             → 18.1% revenue
```

### Tiga Inisiatif Prioritas

1. **Lindungi Champions** — Program VIP dan personalisasi ekstrem untuk mempertahankan 139 customer yang menyumbang 63% revenue.
2. **Selamatkan At Risk** — Campaign re-engagement segera untuk 72 customer bernilai tinggi yang mulai menghilang.
3. **Percepat konversi Loyal → Champions** — Loyalty program berjenjang untuk mendorong 150 Loyal Customers naik satu level.

### KPI yang Disarankan untuk Monitoring

| KPI | Target 6 Bulan |
|-----|----------------|
| Retensi Champions | ≥ 90% |
| Konversi Loyal → Champions | ≥ 30% |
| Re-aktivasi At Risk | ≥ 40% |
| Konversi New → Repeat Buyer | ≥ 50% |
| Pertumbuhan Revenue Yangon | ≥ 10% YoY |
| Penurunan proporsi "Lost" baru | ≤ 15% dari total customer |

### Langkah Selanjutnya

1. **Integrasikan segmen ke CRM** — otomasi tag segmen per customer untuk personalisasi komunikasi.
2. **Bangun monitoring dashboard** — update segmen bulanan untuk mendeteksi pergeseran customer antar segmen.
3. **A/B testing campaign** — uji efektivitas pesan per segmen sebelum deployment massal.
4. **Tambah dimensi analisis** — gabungkan data NPS, kategori produk yang dibeli, dan channel akuisisi untuk RFM yang lebih granular.

---

*Laporan dihasilkan dari notebook `rfm_customer_segmentation.ipynb` | Data: `raw_rfm_sales_transactions__V2_.csv` | Output segmentasi: `rfm_segmented_customers.csv`*
