# Free-to-Paid Conversion Rate Analysis (SQL + Python)

An end-to-end SQL project analyzing student behavior on an e-learning platform — measuring how many free users convert to paid subscribers, and how long that journey takes.

---

## Project Structure

```
free-to-paid-conversion-sql/
│
├── Calculating Free-to-Paid Conversion Rate with SQL Project.sql         # Core SQL queries
├── Calculating Free-to-Paid Conversion Rate with SQL Project.ipynb       # Python notebook with visualizations
├── free_to_paid_conversion_rate.csv    # Output dataset from SQL
└── README.md
```

---

## Business Question

> **What percentage of students who watch free content go on to purchase a subscription — and how long does it take them?**

This is a critical product metric for any freemium platform: understanding the conversion funnel helps optimize marketing, content strategy, and user onboarding.

---

## Database Schema

Three tables are used:

| Table | Key Columns |
|---|---|
| `student_info` | `student_id`, `date_registered` |
| `student_engagement` | `student_id`, `date_watched` |
| `student_purchases` | `student_id`, `date_purchased` |

---

## Methodology

### Step 1 — Build the user journey table
For each student, extract:
- **First date watched** — earliest engagement with free content
- **First date purchased** — earliest subscription purchase
- **Days from registration to first watch** (`days_diff_reg_watch`)
- **Days from first watch to purchase** (`days_diff_watch_purch`)

A key business rule is applied via the `HAVING` clause:
```sql
HAVING first_date_purchased IS NULL
    OR first_date_watched <= first_date_purchased
```
This ensures we only count students whose **first watch came before or on the day of purchase** — filtering out students who purchased without watching first, which would distort the conversion metric.

### Step 2 — Compute the 3 core metrics
```sql
conversion_rate    = COUNT(purchasers) / COUNT(watchers) * 100
avg_reg_watch      = AVG(days from registration to first watch)
avg_watch_purch    = AVG(days from first watch to purchase)
```

---

## Key Results

| Metric | Value |
|---|---|
| Total students analyzed | 16,594 |
| Date range | Dec 2021 – Oct 2022 |

### Distribution Insights (Python / Seaborn KDE plots)

**Days from registration → first watch (`days_diff_reg_watch`)**
| Stat | Value |
|---|---|
| Mean | ~4.1 days |
| Median | 0 days |
| Mode | 0 days |

> Most students watch content on the same day they register — fast activation is a strong signal.

**Days from first watch → purchase (`days_diff_watch_purch`)**
| Stat | Value |
|---|---|
| Mean | ~26.7 days |
| Median | 0 days |
| Mode | 0 days |

> The higher mean (~26.7) reflects a long tail of users who take weeks or months to decide — suggesting retargeting campaigns could recover these delayed converters.

---

## Tools Used

- **MySQL** — SQL queries, joins, aggregations, subqueries
- **Python** — pandas, seaborn for distribution visualization
- **Google Colab** — notebook environment

### SQL Concepts Applied
- Multi-table `JOIN` (inner + left)
- Subqueries (inline views)
- `MIN()` aggregation
- `DATEDIFF()` for time-between-events
- `HAVING` for post-aggregation filtering
- `ROUND()` and `COUNT()` for metric calculation

---

## Business Insights

- **Activation is fast**: the majority of students watch content within hours of registering — suggesting the onboarding experience is effective at driving immediate engagement.
- **Conversion timing is bimodal**: most purchasers convert immediately, but a meaningful segment delays — email nurture sequences could target the 26+ day window.
- **Conversion rate baseline**: the computed rate gives the product team a concrete KPI to A/B test against after any platform changes.

---

## Author

**Khaoula** — Data Science & Intelligent Systems 
📌 Specialization: NLP, Machine Learning, Data Analysis  
🔗 [Tableau Public](https://public.tableau.com/app/profile/khaoula.ghimouze/vizzes) | [Hugging Face](https://huggingface.co/khaoula-ghz)
