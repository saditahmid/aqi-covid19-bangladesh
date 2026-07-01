# Impact of COVID-19 Lockdowns on Air Quality in Bangladesh: Analysis and AQI Forecasting with SVR


[![Dataset](https://img.shields.io/badge/Dataset-CASE%20Project-green)](https://github.com/TSGreen/bangladesh-air-quality)
[![ResearchGate](https://img.shields.io/badge/ResearchGate-Paper-00CCBB?logo=researchgate%26logoColor=white)](https://www.researchgate.net/publication/372261119_Impact_of_COVID-19_Lockdowns_on_Air_Quality_in_Bangladesh_Analysis_and_AQI_Forecasting_with_Support_Vector_Regression)
[![Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-green)](https://www.python.org/)

> **Published at:** *IEEE INCET 2023*
> **Institution:** Independent University, Bangladesh       
> **Authors:** Mohammed Tahmid Hossain, Afra Hossain, Sabrina Masum Meem, Md Fahad Monir, Md Saef Ullah Miah, Talha Bin Sarwar

---

## Overview

This repository contains a **reproducible reconstruction** of our IEEE INCET 2023 paper analyzing Air Quality Index (AQI) trends across **10 major cities in Bangladesh** before, during, and after the COVID-19 lockdown, and forecasting AQI using **Support Vector Regression (SVR)**.

> **⚠️ Reproducibility note:** the original research code was lost; this notebook re-implements the methodology exactly as documented in the paper (Sections III–V).

### Key Findings (from the paper)
- **AQI decreased during the COVID-19 lockdown (2020)** — the highest AQI in 2020 was lower than in 2019 or 2021, and the lockdown-period mean was below the pre-COVID mean
- **AQI is higher from December–March** (dry season, low rainfall)
- **Dhaka is the most consistently polluted major city** (avg AQI 170–190 pre-COVID, ~150 during lockdown, ~250 post-lockdown)
- **SVR achieved MAPE ≈ 6.2%** on the paper's full Jan 2021 – Jun 2022 test window

---

## Results

### SVR Model Performance

| Metric | Paper | This reconstruction |
|:---:|:---:|:---:|
| **MAPE** | **~6.2%** (test: Jan 2021 – Jun 2022) | **~12.5%** (test truncated to Jan–Jun 2021)* |
| Kernel | RBF | RBF |
| gamma | 0.5 | 0.5 |
| C | 10 | 10 |
| epsilon | 0.05 | 0.05 |

\* The current public dataset snapshot ends 2021-06-05, so the reconstruction's test window covers only 131 days, falling almost entirely in the hard-to-forecast dry season. MAPE < 5% is considered perfectly accurate; 10–25% low but acceptable.

![Actual vs Predicted AQI](fig_actual_vs_predicted_aqi.png)

---

## City-Level Analysis

Values summarized from the paper's Section V-B. Per the paper, the dataset lacked pre-2020 records for Narsingdhi and 2020 records for Sylhet, so those bars are marked "no data".

![City AQI Comparison](fig_city_aqi_comparison.png)

![Dhaka AQI](fig_dhaka_aqi.png)

---

## AQI Categories (Table I — Bangladesh Standard)

| AQI Range | Level | Health Implication |
|:---:|:---:|:---|
| 0–50 | Good | Satisfactory, little or no risk |
| 51–100 | Moderate | Acceptable; some concern for sensitive groups |
| 101–150 | Caution | Sensitive groups may experience health effects |
| 151–200 | Unhealthy | Everyone may experience health hazards |
| 201–300 | Very Unhealthy | Entire population may experience serious effects |
| 301–500 | Extremely Unhealthy | Everyone likely to be affected |

---

## Dataset

**Clean Air and Sustainable Environment (CASE) Project**
Ministry of Environment and Forest, Government of Bangladesh
GitHub: https://github.com/TSGreen/bangladesh-air-quality

- **File used:** `data/bronze/case/case_data.csv` (raw; the paper's preprocessing is applied in the notebook). A pre-cleaned alternative exists at `data/silver/case/case_data_clean.csv`.
- **Period (paper):** February 17, 2014 – July 6, 2022 · **Period (current repo snapshot):** February 17, 2014 – June 5, 2021
- **~24,000 rows · 5 columns:** Date · Location · AQI · AQI Category · Range
- **Study area (10 cities, Figs. 3–12):** Barishal · Chittagong · Cumilla · Dhaka · Gazipur · Khulna · Mymensingh · Narsingdhi · Rajshahi · Sylhet (Narayanganj is also present in the data and retained for national statistics)

---

## Methodology

```
CASE Dataset  (raw bronze file · DD-MM-YYYY dates · ~24,000 rows)
        │
        ▼  Phase 1 — Pre-processing
Repair typo'd dates → parse day-first
Drop NaN: Date · Location · AQI · AQI Category  (paper: 13 / 49 / 641 / 63)
Replace DNA values → NaN → drop  (paper: ~5,000)
Drop AQI Range column (insignificant)
Normalize location names (strip CAMS suffixes, fix spellings) → filter to study area
Sort date-wise for time series
        │
        ▼  Phase 1 — Comparative EDA
Split into 3 periods:
  Pre-COVID    : 2014-02-17 → 2019-12-31
  Lockdown     : 2020-01-01 → 2020-12-31
  Post-lockdown: 2021-01-01 → 2021-12-31
Compute max/min/mean/std per period
Resample monthly · yearly · by location
Location-wise plots (AQImean/AQImin/AQImax + period shading)
        │
        ▼  Phase 2 — SVR Forecasting
Train : 2014-02-28 → 2020-12-31
Test  : 2021-01-01 → 2022-06-01  (truncated to 2021-06-05 by current data)
MinMaxScaler(0,1) on AQImean · 7-day lag features
SVR(kernel=RBF, gamma=0.5, C=10, epsilon=0.05)
Inverse transform → MAPE evaluation
```

---

## Repository Structure

```
aqi-covid19-bangladesh/
├── AQI_COVID19_Bangladesh.ipynb        ← main reproducible notebook (executed)
├── requirements.txt
├── README.md
├── .gitignore
├── fig_actual_vs_predicted_aqi.png
├── fig_city_aqi_comparison.png
└── fig_dhaka_aqi.png
```

---

## Setup & Usage

### 1. Clone
```bash
git clone https://github.com/YOUR_GITHUB_USERNAME/aqi-covid19-bangladesh.git
cd aqi-covid19-bangladesh
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Dataset
The dataset is **automatically downloaded** in the notebook from:
```
https://raw.githubusercontent.com/TSGreen/bangladesh-air-quality/master/data/bronze/case/case_data.csv
```

### 4. Run
Open `AQI_COVID19_Bangladesh.ipynb` and run all cells.

---

## Citation

```bibtex
@inproceedings{hossain2023aqi,
  title     = {Impact of COVID-19 Lockdowns on Air Quality in Bangladesh:
               Analysis and AQI Forecasting with Support Vector Regression},
  author    = {Hossain, Mohammed Tahmid and Hossain, Afra and
               Meem, Sabrina Masum and Monir, Md Fahad and
               Miah, Md Saef Ullah and Sarwar, Talha Bin},
  booktitle = {2023 4th International Conference for Emerging Technology (INCET)},
  year      = {2023},
  month     = {May},
  address   = {Belgaum, India},
  publisher = {IEEE},
  doi       = {10.1109/INCET57972.2023.10170636}
}
```

---

## Acknowledgements
- [CASE Project](https://github.com/TSGreen/bangladesh-air-quality) — AQI dataset
- [Scikit-Learn](https://scikit-learn.org/) — SVR implementation
