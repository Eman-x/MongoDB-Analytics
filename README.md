# MongoDB NoSQL Analytics — Volcanoes of Earth & H&M Retail
---
> A two-part NoSQL data-analytics case study built on **MongoDB Atlas**, combining
> **aggregation pipelines** and an interactive **MongoDB Charts** dashboard for a global
> volcanoes dataset with **server-side Atlas Functions** and **index tuning** for a 100k+
> document H&M retail catalog.
> Completed by team **Desert Ninjas** as a project of the **Big Data & AI Bootcamp**
> (Saudi Digital Academy × Coding Dojo Academy).



## Overview

---
> The project demonstrates the full NoSQL workflow on two very different datasets. The first
> applies MongoDB's aggregation framework to explore where volcanoes occur, how exposed
> nearby populations are, and how elevation and eruption history vary by type — then surfaces
> the results in a live dashboard. The second turns a large retail catalog into a query API
> using reusable Atlas Functions and benchmarks query performance before and after indexing.

> The work is organized as two connected parts:

| Part | Dataset | Focus |
|------|---------|-------|
| Part One — Volcanoes of Earth | 2,662 volcano records across 20 countries | Aggregation pipelines + MongoDB Charts dashboard |
| Part Two — H&M Retail | 100,000+ product documents | Atlas Functions (query API) + index performance tuning |



## Dashboard

---
The Part One findings are published as an interactive **MongoDB Charts** dashboard built
directly on the aggregation pipeline outputs.

🔗 **[View the live MongoDB Charts dashboard »](https://charts.mongodb.com/charts-project-0-metjc/dashboards/baa43de5-8782-4b14-92d2-6d4ff641bcf9)**

A static summary of the dashboard insights is included in
[`Part_One_Volcanoes_Of_Earth/Desert_Ninjas_Dashboard_insights.pdf`](Part_One_Volcanoes_Of_Earth/Desert_Ninjas_Dashboard_insights.pdf).



## Technologies Used
---

- MongoDB Atlas (NoSQL document database)
- MongoDB Aggregation Framework
- MongoDB Atlas App Services / Realm Functions
- MongoDB Charts
- Python 3
- PyMongo
- Jupyter Notebook (Google Colab)



## Key Findings
---

**Part One — Volcanoes of Earth** (2,662 records, 20 countries)

- The **United States** recorded the most eruptions of all time with **323 eruptions**;
  after the common era (post-BCE), **Russia** led with **214 eruptions**.
- **Stratovolcanoes** carry the highest population exposure — they have both the highest
  population density within a 5 km radius and the most occurrences (**31**) with more than
  100,000 people living within 10 km.
- **Stratovolcanoes** also show the highest mean elevation, while **submarine** volcanoes
  have the lowest mean elevation (≈ **-5,700 m**, i.e. below sea level).
- Geographic extremes cluster in Japan: **Rakkibetsudake [Demon]** holds the highest mean
  latitude and longitude, **Kobi-sho** the lowest longitude, and **Nikko** the lowest latitude.

**Part Two — H&M Retail** (100,000+ documents)

- Built a reusable **query API** of five Atlas Functions over the product catalog
  (filter by type/colour, case-insensitive search, price-range lookups, and on-the-fly
  discount/new-price calculation).
- Demonstrated measurable **index performance gains**: adding a compound index plus a price
  index drove the same filtered query from an estimated **18 ms down to ~0 ms** execution time.



## Part One — Aggregation Pipelines
---
Five aggregation pipelines were authored against the `volcanoes_of_earth_cleaned` collection,
each writing its results to a dedicated output collection via `$out`.

| # | Title | Stages | Output Collection |
|---|-------|--------|-------------------|
| 1 | Volcanoes located in Japan | `$project` → `$match` | `AllVolcanoesInJapan` |
| 2 | Volcanoes with >5,000 people within 5 km | `$project` → `$match` | `AllVolcanoesWithPW5KMisMoreFiveK` |
| 3 | Volcanoes by country/type with >100,000 people within 10 km | `$project` → `$match` → `$group` | `VolcanoesPerCountryPerVolcanoTypePW10KM` |
| 4 | Volcanoes erupted after the common era, sorted by elevation | `$project` → `$match` → `$sort` | `AllVolcanoesEruptedBeforeBCE` |
| 5 | Highest and lowest elevation by volcano type | `$project` → `$group` (`$max`/`$min`) | `HighestAndLowestElevationByVolcanoTypes` |

Pipeline definitions: [`Part_One_Volcanoes_Of_Earth/`](Part_One_Volcanoes_Of_Earth) — `Pipeline_One.txt` … `Pipeline_Five.txt`.



## Part Two — Functions & Indexes
---
Five MongoDB Atlas Functions expose the H&M catalog as a parameterized, case-insensitive query API.

| # | Function | Purpose |
|---|----------|---------|
| 1 | `Return_Red_OR_Orange` | Returns all red or orange T-shirts |
| 2 | `Return_Index_Color` | Filters by `index_group_name` + `colour_group_name` (case-insensitive) |
| 3 | `ProductDiscount` | Filters by product type, colour, and a price range; returns type, colour, price, department, discount |
| 4 | `Calculate_Discount` | Computes discount and post-discount price for a product type; sorted cheapest-first, limited to 50 |
| 5 | *(text search via index)* | Full-text search over all fields using a text index |

**Indexing strategy** — benchmarked with `.explain()`:

- **Text index** on all fields (`$**`) to enable `$text` keyword search.
- **Compound index** on `product_type_name` + `colour_group_name` for combined filters.
- **Single-field index** on `price` to accelerate price-range queries.

Implementation and benchmarks: [`Part_Two_H&M/H_&_M_desert_ninjas.ipynb`](Part_Two_H&M/H_&_M_desert_ninjas.ipynb)
and [`Part_Two_H&M/NoSqlProject_Indexes.ipynb`](Part_Two_H&M/NoSqlProject_Indexes.ipynb).



## Team Members
---
- Eman Alamari
- Maha Alhazzani
- Reema Alaswad
- Raghad Aleisa
- Aljohara Alkanhal
