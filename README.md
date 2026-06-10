# 🏛️ NYC Government Budget & Expenditure Data Warehouse

> A data warehouse project analyzing New York City government budget and expenditure data from 2013–2024, built as a semester project at **Politeknik Caltex Riau**.

---

**Program:** D4 Teknik Informatika — Politeknik Caltex Riau (TA 2024/2025)

---

## 📌 Project Overview

This project implements a **data warehouse** for analyzing budget management and government expenditure data from New York City. The data spans **11 years (2013–2024)** and covers spending and modified budget metrics across multiple agencies, appropriation types, and MMR (Mayor's Management Report) goals.

---

## 🗂️ Subject & Scope

- **Subject Oriented:** Budget and expenditure management of New York City government
- **Data Duration:** 2013 – 2024 (11 years)
- **Fact Table:** `Fakta_Pengeluaran_Anggaran`

### Measurements
- `jumlah_pengeluaran` — Total expenditure (Previous FY)
- `jumlah_anggaran_dimodifikasi` — Total modified budget (Modified Budget)

---

## 🧩 Dimensional Model

### Dimensions

| Dimension | Key Field | Description |
|-----------|-----------|-------------|
| Waktu (Time) | `waktuID` | Reporting Fiscal Year |
| Agensi (Agency) | `agensiID` | Name of NYC agency |
| Apropriasi (Appropriation) | `apropiasiID` | Type of budget appropriation |
| Destination Category | `destinationCategoryID` | MMR goal category |

### Star Schema

```
                    ┌─────────────┐
                    │    Waktu    │
                    │  (waktuID)  │
                    └──────┬──────┘
                           │
┌─────────────┐    ┌───────▼──────────────┐    ┌──────────────────────┐
│   Agensi    │────│  Fakta_Pengeluaran   │────│     Apropriasi       │
│ (agensiID)  │    │     _Anggaran        │    │  (apropiasiID)       │
└─────────────┘    │                      │    └──────────────────────┘
                   │  - jumlah_pengeluaran│
                   │  - jumlah_anggaran   │    ┌──────────────────────┐
                   │    _dimodifikasi     │────│  DestinationCategory │
                   └──────────────────────┘    │ (destinationCatID)   │
                                               └──────────────────────┘
```

---

## ⚙️ ETL Process

Data cleaning and transformation was performed using **Tableau Prep**:

| Step | Process |
|------|---------|
| 1 | Change data type of `years` column to whole number |
| 2 | Remove `notes` column |
| 3 | Remove null values and dirty data |
| 4 | Create new calculated field: `Destination Category` |
| 5 | Generate dummy `comment` column for sentiment analysis |

### Destination Category Mapping
- **Category 1** → MMR goals starting with `"1a"`
- **Category 2** → MMR goals containing `"2a"`
- **Category 3** → MMR goals containing `"3a"`
- **Category 4** → MMR goals containing `"4a"` or higher
- **Not Applicable** → Goals that don't match any category
- **Others** → Remaining unclassified goals

---

## 🤖 Sentiment Analysis

Sentiment analysis was performed on budget comments using **Python (TextBlob)**:

```python
from textblob import TextBlob

def get_sentiment(comment):
    analysis = TextBlob(comment)
    return analysis.sentiment.polarity  # -1 (negative) to 1 (positive)

def categorize_sentiment(polarity):
    if polarity > 0:
        return 'Positif'
    elif polarity < 0:
        return 'Negatif'
    else:
        return 'Netral'
```

Results were exported to `sentiment_analysis_results.xlsx` and further visualized in Tableau.

---

## 📊 Visualizations (Tableau)

### Expenditure Dashboards
- Expenditure by **Appropriation Type** (pie chart)
- Expenditure by **Destination Category** (bar chart)
- Expenditure by **Year** (bar chart)
- Expenditure by **Top 5 Agencies** (horizontal bar)

### Modified Budget Dashboards
- Modified budget by **Top 5 Agencies**
- Modified budget by **Year**
- Modified budget by **Appropriation Type**
- Modified budget by **Top 3 Destination Categories**

### Interactive Dashboards
- **Dashboard 1:** Modified budget (Top 5 Agencies, Top 3 Goals, Time, Appropriation)
- **Dashboard 2:** Expenditure (Time, Top 5 Agencies, Appropriation, Goals)

Both dashboards are **embedded in a web application** for easy access.

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| Tableau Prep | ETL & data cleaning |
| Tableau Desktop | Visualization & dashboards |
| Python (TextBlob) | Sentiment analysis |
| MySQL | Database storage |
| Web (HTML/CSS) | Dashboard embedding |

---

## 📁 Web UI
<img width="1173" height="817" alt="image" src="https://github.com/user-attachments/assets/649bdbc9-1f5c-4e78-9c93-dfe3661bb2ce" />


```

---

## 🗄️ Database Schema

### Tabel Dimensi Waktu
| Field | Type | Length |
|-------|------|--------|
| waktuID | int | 5 |
| reporting_fiscal_year | varchar | 4 |

### Tabel Dimensi Agensi
| Field | Type | Length |
|-------|------|--------|
| agency_id | int | 5 |
| agency_name | varchar | 255 |

### Tabel Dimensi Apropriasi
| Field | Type | Length |
|-------|------|--------|
| appropriation_id | int | 4 |
| appropriation_type | varchar | 255 |

### Tabel Dimensi Destination Category
| Field | Type | Length |
|-------|------|--------|
| destination_category_id | int | 4 |
| nama_tujuan | varchar | 255 |
| kode_tujuan | varchar | 255 |

---

## 📄 License

This project was created for academic purposes at Politeknik Caltex Riau. All rights reserved by the authors.
