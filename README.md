# Data Analyst & Business Intelligence Repository

Repository ini menyimpan semua dataset dan hasil EDA (Exploratory Data Analysis) untuk keperluan Data Analyst dan Business Intelligence.

## Struktur Folder

```
Datas Analyst/
├── README.md
├── EDA-Documentation.md
├── data/
│   ├── raw/              # Data mentah (belum diproses)
│   └── processed/        # Data bersih (siap pakai)
└── notebooks/            # Jupyter notebooks untuk EDA
```

### Deskripsi Folder

| Folder            | Fungsi                                                      |
| ----------------- | ----------------------------------------------------------- |
| `data/raw/`       | Menyimpan dataset mentah yang belum diproses                |
| `data/processed/` | Menyimpan dataset yang sudah dibersihkan dan siap digunakan |
| `notebooks/`      | Jupyter notebooks untuk exploratory data analysis           |

## Dataset yang Tersedia

| Dataset               | Deskripsi                 | Lokasi      |
| --------------------- | ------------------------- | ----------- |
| Online Retail         | Data transaksi e-commerce | `data/raw/` |
| Retail Sales Data Set | Data penjualan retail     | `data/raw/` |

## Tech Stack

- **Python** - Bahasa pemrograman utama
- **Pandas** - Manipulasi dan analisis data
- **NumPy** - Komputasi numerik
- **Jupyter Notebook** - Interactive analysis
- **Power BI** - Dashboard dan reporting

## Cara Menggunakan

### 1. Setup Folder

```bash
cd "c:\Users\dalleno\Dokumen\Datas Analyst"
```

### 2. Install Dependencies

```bash
pip install pandas numpy matplotlib seaborn jupyter scikit-learn openpyxl
```

### 3. Jalankan EDA

```bash
jupyter notebook
```

Kemudian buka file notebook yang diinginkan (misal: `notebooks/online-retail-EDA.ipynb`)

## Workflow EDA

1. **Simpan data mentah** di folder `data/raw/`
2. **Buat notebook** di folder `notebooks/` untuk explorasi
3. **Proses dan bersihkan data** di notebook
4. **Simpan data bersih** di folder `data/processed/`
5. **Import data bersih ke Power BI** untuk visualisasi dan dashboard

## Template EDA

```python
import pandas as pd
import numpy as np

# Load data
df = pd.read_csv('data/raw/your_dataset.csv')

# Explorasi awal
print(df.info())
print(df.describe())

# Data cleaning
df = df.drop_duplicates()
df = df.fillna(0)

# Simpan data bersih
df.to_csv('data/processed/your_dataset_clean.csv', index=False)
```

## Dokumentasi EDA

Untuk dokumentasi lengkap proses EDA, lihat file [EDA-Documentation.md](EDA-Documentation.md)

## License

Repository ini untuk keperluan pembelajaran dan analisis internal.

---

**Dibuat untuk:** Data Analyst & Business Intelligence Projects  
**Tools:** Python, Pandas, Jupyter Notebook, Power BI
