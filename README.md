# 🛒 Retail Store Sales EDA

## 📌 Project Overview

This project analyzes weekly retail sales data across **50 stores, 20 departments, and 3 years** (2022–2024) to identify the structural drivers of sales performance and understand how store characteristics, seasonal events, and department mix shape revenue patterns.

Rather than treating all stores as identical, the analysis benchmarks performance by **store type, region, and holiday periods**, making the segmentation adaptive and data-driven.

This is the first project in this portfolio. Where a future project will tackle a seasonal trend analysis, this one examines what drives performance across store formats and departments.

---

## 🗃️ Dataset

The analysis uses three files merged on `store_id` and `date`.

**Key columns used:**

| Column | Description |
|---|---|
| `weekly_sales` | Revenue for a given store-department-week |
| `store_type` | `A`, `B`, or `C` (A = largest format) |
| `store_size` | Square footage |
| `region` | `North`, `East`, `South`, `West` |
| `department` | 1–20, representing merchandise categories |
| `is_holiday` | Boolean flag for holiday weeks |
| `holiday_name` | Specific holiday (null for non-holiday weeks) |
| `markdown_1`–`markdown_5` | Promotional markdown amounts |

**Coverage:** 2022-01-01 to 2024-12-31 | 50 Stores | 20 Departments | 157 weeks

---

## 🔍 The Investigation

### 1. Data Integration & Profiling

Three CSVs are merged via `store_id` and `date`, then column names are standardized to title case. Dtype correction is applied, `date` is cast from string to `datetime64[us]`. A single profiling pass captures row counts, date range, unique stores/departments, and missing values.

**Dataset snapshot:**

| Metric | Value |
|---|---|
| Total rows | 156,000 |
| Date range | 2022-01-01 to 2024-12-21 |
| Unique weeks | 156 |
| Stores | 50 |
| Departments | 20 |
| Missing values | Only `holiday_name` (expected: null = non-holiday) |

⚠️ Markdown columns are sparse, mostly null outside promotion periods, so their correlation with sales may appear weaker than their true business impact.

---

### 2. Weekly Sales Distribution

Sales are analyzed via histogram and boxplot to understand the shape of the data and detect outliers.

| Statistic | Value |
|---|---|
| Mean | $56K |
| Median | $43K |
| Max | $506K |

![Weekly Sales Distribution](https://github.com/M-atere/Retail-Store-Sales-EDA/blob/main/Visualizations/EDA%2001%20Sales%20Distribution.png)

**Insight:** Sales are right-skewed, the median sits well below the mean, meaning a small number of high-performing department-weeks pull the average up. The $506K maximum likely comes from a large Type A store during a holiday week.

---

### 3. Store Type as the Dominant Driver

Instead of a global average, sales are grouped by `store_type` to reveal structural performance gaps.

| Store Type | Avg Weekly Sales | Relative Performance |
|---|---|---|
| **Type A** | $80K | Baseline (largest format) |
| **Type B** | $38K | **-52%** vs Type A |
| **Type C** | $16K | **-80%** vs Type A |

![Store Type](https://github.com/M-atere/Retail-Store-Sales-EDA/blob/main/Visualizations/EDA%2002%20Sales%20By%20Store%20Type.png)

**Insight:** Store type is the strongest predictor in the dataset. The 5x gap between Type A and Type C suggests format-driven assortment, foot traffic, or geographic placement effects. This will be a critical feature in any forecasting model.

---

### 4. Regional Variation (Macro → Micro)

Sales are grouped by `region` to test whether geography creates secondary performance tiers.

| Region | Avg Weekly Sales | Character |
|---|---|---|
| **North** | $61K | Highest average |
| **East** | $60K | Close second |
| **South** | $52K | Mid-tier |
| **West** | $52K | Mid-tier |

**Insight:** Regional variation is meaningful (~17% spread between North and South/West) but less extreme than store-type differences. North and East likely reflect higher-density or higher-income markets.

---

### 5. Holiday Effect

Holiday weeks are benchmarked against non-holiday weeks using conditional grouping.

| Period | Avg Weekly Sales | Lift |
|---|---|---|
| Non-Holiday | Baseline | — |
| Holiday | +60% | ⬆️ Above Average Peak |

![Holiday Effect](https://github.com/M-atere/Retail-Store-Sales-EDA/blob/main/Visualizations/EDA%2004%20Holiday%20Sales.png)

**Insight:** Holiday weeks generate **60% more sales** on average. This is one of the strongest signals in the dataset and will be a key feature in the prediction model. The effect likely interacts with store type.

---

### 6. Department Concentration

Average weekly sales rank departments to identify revenue concentration.

**Top 10 departments:**

| Rank | Department | Avg Weekly Sales |
|---|---|---|
| 1 | 20 | $76K |
| 2 | 19 | $73K |

![Department Concentration](https://github.com/M-atere/Retail-Store-Sales-EDA/blob/main/Visualizations/EDA%2005%20Top%20Departments.png)

**Insight:** Department 20 averages $76K/week, roughly **5x the dataset average**. The top 10 departments drive the majority of revenue, suggesting opportunities for portfolio optimisation (e.g., expanding high-performing departments in underperforming stores).

---

### 7. Store Size vs. Sales Correlation

Store size is tested as a continuous predictor using scatter plot with a trendline.

| Metric | Value |
|---|---|
| Correlation (r) | 0.586 |
| Relationship | Moderate positive |

**Insight:** Larger stores sell more, but the relationship is not perfectly linear. The scatter suggests other factors; region, department mix, or local competition, also play significant roles. Store size alone is insufficient to predict sales.

---

### 8. Feature Correlation Heatmap

A lower-triangle correlation matrix visualizes linear relationships among numeric features.

**Key correlations with `weekly_sales`:**

| Feature | Correlation | Interpretation |
|---|---|---|
| `store_size` | 0.59 | Moderate positive (as above) |
| `is_holiday` | 0.35 | Holiday weeks boost sales |
| `markdown_1`–`markdown_5` | Weak | Sparse data masks true effect |
| `temperature` | Weak | Minimal linear relationship |
| `fuel_price` | Weak | Minimal linear relationship |

**Insight:** The first row/column of the heatmap confirms store size and holiday status as the strongest linear predictors. Markdown correlations appear weak due to sparsity, mostly outside promotion periods, so their true business impact may be understated.

---

## 💡 Key Takeaways

- **Store type is the dominant structural driver.** The 5x performance gap between Type A and Type C stores dwarfs regional and holiday effects. Any forecasting model must treat store type as a primary feature.
- **Holiday weeks are high-leverage events.** The +60% sales lift is consistent and actionable for inventory planning, staffing, and promotional timing.
- **Regional variation exists but is secondary.** North and East outperform South and West, but the gap is modest compared to store-type differences. Regional strategy should focus on format optimization, not just geography.
- **Department mix creates revenue concentration.** The top 10 departments (of 20) drive disproportionate sales, with Department 20 averaging 5x the dataset mean. Portfolio rebalancing may unlock growth.
- **Store size matters, but not linearly.** The r = 0.586 correlation suggests diminishing returns or interaction effects with department mix and region.
- **Markdown data is sparse.** Weak correlations may reflect data coverage, not business irrelevance. A dedicated promotion-effectiveness analysis would require denser markdown records.

---

## 🛠️ Python Techniques Used

- **`pd.merge()`** — multi-source data integration on composite keys
- **`pd.to_datetime()`** — dtype correction for temporal analysis
- **`.groupby()` + `.agg()`** — grouped statistical summarization
- **`.corr()`** — Pearson correlation matrix
- **`sns.set_theme()`** — consistent visualization styling
- **`matplotlib.ticker.FuncFormatter`** — custom axis formatting ($/K)
- **`np.polynomial.polynomial.polyfit()`** — trendline estimation
- **`np.triu()` + `sns.heatmap()`** — masked correlation visualization
- **Conditional annotation** — business insight overlay on charts

---

## 🗂️ Repository Structure

```
retail-sales-eda/
│
├── notebooks/
│   └── retail_sales_eda.ipynb      # Full analysis notebook
│
├── Visualizations/
│   ├── EDA 01 Sales Distribution.png
│   ├── EDA 02 Sales By Store Type.png
│   ├── EDA 03 Sales by Region.png
│   ├── EDA 04 Holiday Sales.png
│   ├── EDA 05 Top Departments.png
│   ├── EDA 06 Size vs Sales.png
│   └── EDA 07 Correlation Heatmap.png
│
└── README.md
```

---

## 📂 Data Provenance

- **Data Source:** [Kaggle](https://www.kaggle.com/datasets/noopurbhatt/retail-store-sales-forecasting-dataset?select=README.md)
- **Environment:** Python 3.14.4, Jupyter Notebook
- **Related Project:** [Future: Seasonal Trend Analysis]
