# GooglePlayStore-EDA-Feature-Engineering
Analyzed 10,800+ Google Play Store apps using Python, pandas, and seaborn. Handled messy real-world data — mixed units, corrupt rows, embedded special characters — then explored category distributions, install patterns, and rating behavior to surface actionable market insights.
---

## Dataset

- **Source:** [Google Play Store Dataset](https://raw.githubusercontent.com/krishnaik06/playstore-Dataset/main/googleplaystore.csv)
- **Size:** ~10,841 rows × 13 columns (post-cleaning)
- **Features:** App name, category, rating, reviews, size, installs, type (free/paid), price, content rating, genres, last updated, current version, Android version

---

## Stack

| Library | Purpose |
|---|---|
| `pandas` | Data loading, cleaning, transformation |
| `numpy` | Null handling, numerical operations |
| `matplotlib` | Base plotting |
| `seaborn` | Statistical visualizations |

---

## Data Cleaning

The raw dataset had significant quality issues across multiple columns. Here's what was fixed:

**Reviews** — All values were stored as strings. One row (index 10472) had a value of `"3.0M"` instead of a plain integer, which caused dtype conversion to fail silently. That row was dropped and the column was cast to `int`.

**Size** — Mixed units across rows: some values in MB (suffixed `M`), others in KB (suffixed `k`), and some rows just said `"Varies with device"`. Standardized everything to a single numeric scale (KB-equivalent), with `"Varies with device"` replaced by `NaN` and the column cast to `float`.

**Installs & Price** — Both columns had formatting characters embedded in the values (`+`, `,`, `$`). Stripped all of these out and cast `Installs` to `int` and `Price` to `float`.

**Last Updated** — Stored as a plain string. Parsed to `datetime` and extracted `Day`, `Month`, and `Year` as separate integer columns.

**Duplicates** — Found 1,181 duplicate app entries. Kept the first occurrence of each, bringing the dataset down to 9,660 rows.

A working copy (`df_copy`) was used for all transformations to keep the original DataFrame intact. The cleaned dataset was saved to CSV after this stage.

---

## Feature Engineering

- Decomposed `Last Updated` into three usable numeric columns: `Day`, `Month`, `Year`
- Separated all columns into numeric and categorical feature lists for streamlined downstream analysis
  - **Numeric (9):** Rating, Reviews, Size, Installs, Price, Day, Month, Year, Last Updated
  - **Categorical (7):** App, Category, Type, Content Rating, Genres, Current Ver, Android Ver

---

## EDA

**Distributions**

KDE plots across numeric features showed clear skew patterns. `Rating` and `Year` are left-skewed, while `Reviews`, `Size`, `Installs`, and `Price` are all heavily right-skewed — a small number of apps dominate on every metric, which is expected for an app marketplace.

**Category Breakdown**

Family (~18%) and Games (~11%) account for the largest share of apps by count. Beauty, Comics, and Arts-related categories together make up less than 1% of the catalog.

When looking at installs rather than count, Games pull far ahead — roughly 35 billion cumulative installs, more than any other category by a significant margin.

**Top Apps by Category**

Identified the 5 most-installed apps in each of four major categories:

| Category | Top App |
|---|---|
| Game | Subway Surfers |
| Communication | Hangouts |
| Productivity | Google Drive |
| Social | Instagram |

**Ratings**

271 apps have a perfect 5.0 rating on the Play Store. The highest-rated app by install count is *CT Brain Interpretation*, listed under the Family category.

---

## Key Findings

- Install distribution is extremely long-tailed — a handful of apps account for the vast majority of installs across every category
- Gaming dominates by installs despite not being the largest category by app count
- The free-to-paid ratio is heavily skewed toward free apps
- The raw dataset requires non-trivial cleaning before it's usable — type issues, inconsistent units, and embedded formatting are present across most columns

---

## Project Structure

```
├── playstore_eda.ipynb
├── data/
│   └── google_cleaned.csv   # Saved after cleaning
└── README.md
```

---

## Setup

```bash
pip install pandas numpy matplotlib seaborn
jupyter notebook playstore_eda.ipynb
```

The dataset is fetched directly from a public URL inside the notebook — no local file needed to get started.
