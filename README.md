# 🚕 NYC Taxi Analytics

A Power BI project exploring 12 million taxi trips across New York City

---

**Dataset:** [NYC Yellow Taxi Trip Data — January 2015](https://www.kaggle.com/datasets/elemento/nyc-yellow-taxi-trip-data?select=yellow_tripdata_2015-01.csv) (~12 million rows)

---

## Key questions to answer

- When are taxis actually busiest — and where?
- What does the pricing structure look like across different trip types?
- Is there anything interesting hiding in the tip data?

---

## 💡 What I Found

- **Friday at 7 PM in Midtown** is peak demand — you can see it clearly in the heat map
- **Credit card passengers tip ~24%** on average; cash tips are essentially unrecorded (they go straight to the driver)
- **Airport trips are only 2% of volume** but average $52 vs. $11 for a regular ride — nearly 5× more
- If even a portion of cash rides shifted to card, it would represent a **~$45M swing in tracked revenue** in tips

---

## 📊 The Dashboards

| Dashboard | What It Shows |
|---|---|
| **Overview** | Total trips, revenue, fares, and daily trends |
| **Demand Patterns** | Hour × day heat map, pickup location density across NYC |
| **Pricing & Tips** | Fare vs. distance, tip behavior by payment type |

---

## 🛠️ How It Was Built

- **Power BI Desktop** — all three dashboards
- **Power Query** — data cleaning (removed zero fares, coordinate outliers, impossible distances)
- **DAX** — 20+ custom measures including tip %, revenue per mile, and time-based aggregations
- **Star schema** — one fact table (Trips) + four dimension tables (Date, Time, RateCode, PaymentType)

---

## 📁 Repo Structure

```
📂 dashboards/     → .pbix file
📂 screenshots/    → dashboard previews
📂 documentation/  → data dictionary + DAX formulas
📂 data/           → download instructions (dataset is ~2GB)
```

---

## 🚀 How to View This Project

### Quick View (No Software Needed):
1. Check out the **PDF** in the `dashboards` folder
2. Browse the **screenshots** to see each dashboard
3. Read the **documentation** for technical details

### Interactive Version:
Want to explore the full Power BI file?
- Email me at: your.email@example.com
- I'll share the .pbix file via Google Drive

**Note:** The .pbix file exceeds GitHub's size limits, so it's shared separately.

---

## 📬 Get In Touch

Always happy to connect — whether it's feedback on this project, talking data, or exploring opportunities.

**Email:** [syedawaiswaseem@gmail.com]
**LinkedIn:** [https://www.linkedin.com/in/syed-awais-waseem/]
