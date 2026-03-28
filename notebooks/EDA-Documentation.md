# EDA Online Retail - Documentation

Dokumentasi lengkap untuk Exploratory Data Analysis (EDA) dataset Online Retail.

---

## Daftar Isi

1. [Overview](#overview)
2. [Dataset](#dataset)
3. [Proses Data Cleaning](#proses-data-cleaning)
4. [Feature Engineering](#feature-engineering)
5. [Key Insights](#key-insights)
6. [Struktur File](#struktur-file)
7. [Cara Menggunakan](#cara-menggunakan)

---

## Overview

Proyek ini melakukan Exploratory Data Analysis (EDA) pada dataset transaksi e-commerce Online Retail. Tujuan analisis adalah untuk memahami pola penjualan, perilaku customer, dan karakteristik data sebelum digunakan untuk dashboard Power BI.

### Tujuan EDA

- Memahami struktur dan kualitas data
- Identifikasi missing values dan anomalies
- Membersihkan data dari duplikasi dan inkonsistensi
- Ekstrak insight bisnis yang actionable
- Menyiapkan data bersih untuk visualisasi di Power BI

---

## Dataset

### Sumber

Dataset Online Retail dari UCI Machine Learning Repository.

### Lokasi File

| Tipe         | Lokasi                                   |
| ------------ | ---------------------------------------- |
| Raw Data     | `data/raw/Online Retail.xlsx`            |
| Cleaned Data | `data/processed/online_retail_clean.csv` |

### Struktur Data Awal

| Kolom       | Tipe     | Deskripsi                    |
| ----------- | -------- | ---------------------------- |
| InvoiceNo   | String   | Nomor invoice unik (6 digit) |
| StockCode   | String   | Kode produk unik (5-6 digit) |
| Description | String   | Nama produk                  |
| Quantity    | Integer  | Jumlah barang per transaksi  |
| InvoiceDate | DateTime | Tanggal dan waktu transaksi  |
| UnitPrice   | Float    | Harga per unit (dalam GBP)   |
| CustomerID  | Float    | ID customer unik (5 digit)   |
| Country     | String   | Nama negara customer         |

### Statistik Awal

- **Total Baris:** 541.909 transaksi
- **Total Kolom:** 8 fitur
- **Periode Data:** Desember 2010 - Desember 2011
- **Missing Values:**
  - Description: 1.454 baris (0.27%)
  - CustomerID: 135.080 baris (24.9%)

---

## Proses Data Cleaning

### Step 1: Handling Missing Values

**Masalah:**

- Kolom `Description` memiliki 1.454 missing values (0.27%)
- Kolom `CustomerID` memiliki 135.080 missing values (24.9%)

**Solusi:**

```python
df['Description'] = df['Description'].fillna('Unknown')
df['CustomerID'] = df['CustomerID'].astype(str).fillna('Guest')
```

**Alasan:**

- `Description` → Diisi dengan "Unknown" karena persentase kecil
- `CustomerID` → Diisi dengan "Guest" karena customer tanpa ID adalah guest/walk-in customer

---

### Step 2: Removing Exact Duplicates

**Masalah:**
Ditemukan 5.268 baris yang identik (semua kolom sama persis).

**Solusi:**

```python
exact_dups = df.duplicated().sum()
df = df.drop_duplicates()
```

**Alasan:**
Duplikat identik kemungkinan adalah entry error yang dapat menginflasi hasil analisis.

---

### Step 3: Aggregating Data

**Masalah:**
Ditemukan 10.684 baris dengan `InvoiceNo + StockCode` yang sama, tetapi dengan `Quantity` berbeda. Ini menunjukkan customer membeli produk yang sama beberapa kali dalam satu invoice (entry terpisah).

**Contoh:**
| InvoiceNo | StockCode | Quantity |
|-----------|-----------|----------|
| 536412 | 21448 | 2 |
| 536412 | 21448 | 1 |
| 536412 | 21448 | 1 |

**Solusi:**

```python
df = df.groupby(['InvoiceNo', 'StockCode'], as_index=False).agg({
    'Description': 'first',
    'Quantity': 'sum',
    'InvoiceDate': 'first',
    'UnitPrice': 'first',
    'CustomerID': 'first',
    'Country': 'first'
})
```

**Hasil:**

- Data lebih akurat (Quantity di-sum untuk produk yang sama dalam invoice yang sama)
- Jumlah baris berkurang signifikan

---

### Step 4: Creating Revenue Column

**Tujuan:**
Membuat kolom Revenue untuk analisis penjualan.

**Solusi:**

```python
df['Revenue'] = df['Quantity'] * df['UnitPrice']
```

**Manfaat:**

- Memudahkan analisis total penjualan per transaksi
- Dapat digunakan untuk segmentasi customer dan produk

---

## Feature Engineering

### Step 5: Extracting Date Features

**Tujuan:**
Ekstrak komponen tanggal untuk analisis temporal di Power BI.

**Fitur Baru:**

```python
df['InvoiceDate'] = pd.to_datetime(df['InvoiceDate'])
df['Year'] = df['InvoiceDate'].dt.year
df['Month'] = df['InvoiceDate'].dt.month
df['Day'] = df['InvoiceDate'].dt.day
df['Hour'] = df['InvoiceDate'].dt.hour
df['DayOfWeek'] = df['InvoiceDate'].dt.day_name()
df['MonthName'] = df['InvoiceDate'].dt.month_name()
```

**Manfaat:**
| Fitur | Kegunaan Analisis |
|-------|-------------------|
| Year | Trend tahunan |
| Month | Trend bulanan, seasonality |
| Day | Pattern harian |
| Hour | Jam sibuk transaksi |
| DayOfWeek | Perbandingan weekday vs weekend |
| MonthName | Visualisasi nama bulan |

---

## Key Insights

### General Statistics

| Metric                    | Value                             |
| ------------------------- | --------------------------------- |
| Total Transactions        | Jumlah invoice unik               |
| Total Products            | Jumlah produk unik                |
| Total Customers           | Jumlah customer (excl. Guest)     |
| Guest Customers           | Jumlah transaksi tanpa CustomerID |
| Total Revenue             | Total pendapatan (GBP)            |
| Average Transaction Value | Rata-rata nilai per transaksi     |

### Top Countries

5 negara dengan revenue tertinggi (untuk divisualisasikan di Power BI)

### Top Products

5 produk dengan revenue tertinggi (untuk divisualisasikan di Power BI)

### Peak Sales

- **Peak Month:** Bulan dengan penjualan tertinggi
- **Peak Day:** Hari dengan penjualan tertinggi
- **Peak Hour:** Jam dengan penjualan tertinggi

---

## Struktur File

```
Datas Analyst/
├── README.md
├── EDA-Documentation.md
├── data/
│   ├── raw/
│   │   └── Online Retail.xlsx
│   └── processed/
│       └── online_retail_clean.csv
└── notebooks/
    └── online-retail-EDA.ipynb
```

### Deskripsi Output

| File                      | Deskripsi                                          |
| ------------------------- | -------------------------------------------------- |
| `online-retail-EDA.ipynb` | Notebook lengkap dengan data cleaning dan analisis |
| `online_retail_clean.csv` | Data bersih siap pakai untuk Power BI              |

### Kolom Data Bersih

| Kolom       | Tipe     | Deskripsi                          |
| ----------- | -------- | ---------------------------------- |
| InvoiceNo   | String   | Nomor invoice                      |
| StockCode   | String   | Kode produk                        |
| Description | String   | Nama produk                        |
| Quantity    | Integer  | Jumlah barang (sudah agregasi)     |
| InvoiceDate | DateTime | Tanggal transaksi                  |
| UnitPrice   | Float    | Harga per unit (GBP)               |
| Revenue     | Float    | Total revenue per baris            |
| CustomerID  | String   | ID customer (Guest jika tidak ada) |
| Country     | String   | Negara customer                    |
| Year        | Integer  | Tahun transaksi                    |
| Month       | Integer  | Bulan (1-12)                       |
| Day         | Integer  | Hari (1-31)                        |
| Hour        | Integer  | Jam (0-23)                         |
| DayOfWeek   | String   | Nama hari                          |
| MonthName   | String   | Nama bulan                         |

---

## Cara Menggunakan

### Prerequisites

```bash
pip install pandas numpy openpyxl
```

### Menjalankan Notebook

1. Buka Jupyter Notebook:

   ```bash
   jupyter notebook
   ```

2. Buka file `notebooks/online-retail-EDA.ipynb`

3. Jalankan semua cell secara berurutan:
   - Kernel → Restart & Run All

### Output

Setelah notebook selesai dijalankan:

1. **Key insights** ditampilkan di output cell
2. **Data bersih** tersimpan di `data/processed/online_retail_clean.csv`

### Import ke Power BI

1. Buka Power BI Desktop
2. Klik **Get Data** → **Text/CSV**
3. Pilih file `data/processed/online_retail_clean.csv`
4. Klik **Load**
5. Buat visualisasi yang diinginkan

### Menggunakan Data Bersih (Python)

```python
import pandas as pd

# Load cleaned data
df_clean = pd.read_csv('data/processed/online_retail_clean.csv')

# Contoh analisis
print(df_clean.head())
print(df_clean.groupby('Country')['Revenue'].sum().sort_values(ascending=False))
```

---

## Catatan Penting

### Data Quality Issues yang Ditemukan

1. **Missing CustomerID** (24.9%) - Customer tanpa ID (guest)
2. **Exact Duplicates** (5.268 baris) - Entry error
3. **Split Entries** (10.684 baris) - Produk sama dalam invoice yang sama di-entry terpisah

### Asumsi yang Digunakan

1. Missing `CustomerID` adalah guest customer, bukan data hilang
2. Duplikat identik adalah error dan harus dihapus
3. Entry terpisah untuk produk yang sama dalam invoice yang sama harus diagregasi (sum Quantity)

### Rekomendasi untuk Analisis Lanjutan di Power BI

1. **RFM Analysis** - Segmentasi customer berdasarkan Recency, Frequency, Monetary
2. **Cohort Analysis** - Retention analysis per cohort bulan
3. **Geographic Analysis** - Peta penjualan per negara
4. **Time Series Analysis** - Trend dan forecast penjualan
5. **Product Analysis** - Pareto chart, product performance

---

## Referensi

- Dataset: https://archive.ics.uci.edu/ml/datasets/online+retail
- Pandas Documentation: https://pandas.pydata.org/docs/
- Power BI Documentation: https://docs.microsoft.com/power-bi/

---

**Dokumentasi ini dibuat untuk:** Data Analyst & Business Intelligence Projects  
**Tools:** Python, Pandas, Jupyter Notebook, Power BI  
**Terakhir Update:** 2026-03-28
