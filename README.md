# HR Employee Details Dashboard – Power BI

An end-to-end Power BI project that analyzes a 183-employee HR dataset across India and New Zealand — covering headcount, gender mix, age spread, salary bands, performance ratings, and hiring trends — built with a star-schema style data model, DAX measures, and interactive slicers.

![Power BI](https://github.com/LikithReddy128/-HR-Employee-Details-Analysis-Dashboard/blob/main/Hr%20Employee%20Details%20Analysis.pbix)
![Excel](https://github.com/LikithReddy128/-HR-Employee-Details-Analysis-Dashboard/blob/main/hr-data.xlsx)
![DAX](https://img.shields.io/badge/DAX-Measures-blue?style=for-the-badge)

---
![Power BI](https://github.com/LikithReddy128/-HR-Employee-Details-Analysis-Dashboard/blob/main/Hr%20Employee%20Details%20Analysis.png)

## Table of Contents
1. [Project Overview](#project-overview)
2. [Repository Structure](#repository-structure)
3. [Dataset](#dataset)
4. [Data Model](#data-model)
5. [KPIs & DAX Measures](#kpis--dax-measures)
6. [Dashboard Pages](#dashboard-pages)
7. [Slicers Used & How to Add Them](#slicers-used--how-to-add-them)
8. [How to Open / Use This Project](#how-to-open--use-this-project)
9. [How This Repository Was Created (Git & GitHub Steps)](#how-this-repository-was-created-git--github-steps)
10. [Insights & Outcomes](#insights--outcomes)
11. [Tech Stack](#tech-stack)
12. [Author](#author)

---

## Project Overview

**Aim / Goal:** Build a self-service HR analytics dashboard that lets stakeholders compare workforce metrics (headcount, salary, performance, tenure) between the **India** and **New Zealand** offices, filter by department/gender/rating, and spot trends in hiring and pay without needing to open the raw spreadsheet.

**Business questions this dashboard answers:**
1. How many people are there in each department?
2. What is the gender distribution by department?
3. What is the age spread of our staff (histogram)?
4. What is the min / max / average salary in each department?
5. Who are the top earners in each country?
6. What does the performance spread look like (sorted meaningfully, not alphabetically)?
7. What is the company's growth (hiring) trend over time?
8. Can employees be filtered/searched by the starting letter of their name?
9. Is there a relationship between performance rating and salary?
10. How do India and New Zealand compare — a quick side-by-side scorecard?

---

## Repository Structure

```
HR-Employee-Details-Dashboard/
│
├── README.md                        # You are here – full project summary
├── Documentation.md                 # Detailed write-up: aim, cleaning steps, results, insights
│
├── original-data/
│   └── hr-data.xlsx                 # Raw/source HR dataset (183 employees, 8 columns)
│
├── data-model/
│   ├── conceptual-model.png         # High-level entity relationship view
│   └── physical-data-model.png      # Power BI model view (tables, columns, relationships)
│
├── power-bi-file/
│   └── HR_Employee_Details_Analysis.pbix   # The working Power BI report
│
└── screenshots/
    └── dashboard-preview.png        # Exported image of the final dashboard (India & NZ pages)
```

> This structure mirrors the standard six items every Power BI / data-analytics GitHub submission should contain: **original data, conceptual model, physical data model, the .pbix file, documentation, and a README.**

---

## Dataset

| Property | Detail |
|---|---|
| File |  [hr-data.xlsx](https://github.com/LikithReddy128/-HR-Employee-Details-Analysis-Dashboard/blob/main/hr-data.xlsx) |
| Rows | 183 employees |
| Columns | `Name`, `Gender`, `Age`, `Rating`, `Date Joined`, `Department`, `Salary`, `Country` |
| Countries | India (`IND`), New Zealand (`NZ`) |
| Departments | Finance, HR, Procurement, Sales, Website |
| Gender values | Male, Female, Other |
| Rating values | Very poor, Poor, Average, Above average, Exceptional |
| Age range | 19 – 46 |
| Date Joined range | 07-May-2020 – 29-Apr-2023 |
| Salary | Numeric, no currency symbol in source (assumed local currency per country) |

---

## Data Model

The model uses one primary fact/dimension-hybrid table plus a small support table for custom sorting:

**`Data` (main table)**
| Column | Type | Notes |
|---|---|---|
| Name | Text | Employee full name |
| Gender | Text | Male / Female / Other |
| Age | Whole number | |
| Age (bins) | Grouped column | Age grouped into bins for the histogram (e.g. 15-yr / 5-yr buckets) |
| Rating | Text | Performance rating category |
| Date Joined | Date | Used for hiring/growth trend |
| Department | Text | Finance, HR, Procurement, Sales, Website |
| Salary | Whole number | Base compensation |
| Salary1 | Measure/column | Secondary salary field used for an independently-formatted visual (e.g. a table that needed different number formatting/sorting than the base `Salary` column) |
| Country | Text | IND / NZ |
| First Letter | Calculated column | `= LEFT(Data[Name], 1)` — powers the "filter employees by starting letter" slicer |
| Head Count | Measure | `= COUNTROWS(Data)` |
| Max Salary | Measure | `= MAX(Data[Salary])` |
| Min Salary | Measure | `= MIN(Data[Salary])` |
| Average Salary | Measure | `= AVERAGE(Data[Salary])` |

**`field order` (support table)**
| Column | Purpose |
|---|---|
| Rating | Duplicate list of the Rating categories |
| Order | Numeric rank (1 = Very poor … 5 = Exceptional) used as a **"Sort by Column"** so the Rating axis displays in logical order instead of A–Z |

**Conceptual Model** (`data-model/conceptual-model.png`): shows `Data` as the single employee entity, with `Country` and `Department` as the natural grouping dimensions and `field order` as a lookup table related to `Data[Rating]`.

**Physical Data Model** (`data-model/physical-data-model.png`): the actual Power BI **Model view** screenshot — one relationship, `field order[Rating] → Data[Rating]` (one-to-many), used purely for sorting, not filtering.

> *Note: the two model images should be exported from Power BI's Model view (for the physical model) and drawn in draw.io / Lucidchart / PowerPoint (for the conceptual model), then saved into `data-model/` with the exact file names above.*

---

## KPIs & DAX Measures

| KPI Card | DAX |
|---|---|
| Head Count | `Head Count = COUNTROWS(Data)` |
| Max Salary | `Max Salary = MAX(Data[Salary])` |
| Min Salary | `Min Salary = MIN(Data[Salary])` |
| Average Salary | `Average Salary = AVERAGE(Data[Salary])` |

These four measures drive every card, table, and chart on both country pages.

---

## Dashboard Pages

The report has (at minimum) two pages, styled with a distinct background color per country for quick visual identification:

**🇮🇳 India (orange theme)**
- Head Count card → **92**
- Max Salary card → **119K**
- "Head count by Department and Country" donut chart (Procurement, Website, Finance, Sales, HR)
- Min/Max Salary by Department table

**🇳🇿 New Zealand (green theme)**
- Head Count card → **91**
- Max Salary card → **119K**
- Matching donut chart and salary table for direct comparison

Together the two pages answer question #10 (India vs. New Zealand scorecard) simply by placing them side by side or toggling between tabs.

Additional visuals to complete the 10 analysis themes:
- **Bar chart** – Headcount by Department (Q1)
- **Stacked/clustered bar** – Gender distribution by Department (Q2)
- **Histogram** – Age (bins) on the axis, Head Count as the value (Q3)
- **Table** – Department, Min Salary, Max Salary, Average Salary (Q4)
- **Sorted table** – Top earners, sorted descending by Salary, sliced by Country (Q5)
- **Bar chart sorted by `field order[Order]`** – Rating spread (Q6)
- **Line chart** – Date Joined (by Year) vs. Head Count, to show growth trend (Q7)
- **Slicer/search box** on `First Letter` – Employee filter (Q8)
- **Scatter chart** – Rating (or Order) vs. Salary (Q9)

---

## Slicers Used & How to Add Them

**Slicers included in this report:**
- **Country** (IND / NZ) — or built as two separate pages as shown above
- **Department** (Finance, HR, Procurement, Sales, Website)
- **Gender** (Male, Female, Other)
- **Rating** (sorted via the `field order` table)
- **Age (bins)**
- **First Letter** (A–Z, for the employee name search/filter)

### Step-by-step: how to add a slicer in Power BI Desktop
1. Open the `.pbix` file in **Power BI Desktop**.
2. On the report canvas, go to the **Visualizations** pane and click the **Slicer** icon (funnel-shaped icon).
3. With the empty slicer placeholder still selected, drag the field you want to filter by (e.g. `Department`) from the **Data** pane into the **Field** well of the slicer.
4. Resize/reposition the slicer on the canvas.
5. Format it: select the slicer → **Format visual** pane →
   - **Slicer settings → Options** to switch style between *List*, *Dropdown*, or *Tile*.
   - **Selection** controls to allow single-select or multi-select and to show a "Select all" option.
6. Repeat for each additional slicer field (`Gender`, `Rating`, `Age (bins)`, `Country`, `First Letter`).
7. To keep slicers consistent across multiple report pages, select the slicer → **View** ribbon → **Sync slicers** → tick the pages you want it synced to.
8. For the `First Letter` slicer, using the **Dropdown** style with search enabled gives the cleanest "type a letter to filter" experience.
9. Save the file (`Ctrl + S`).

---

## How to Open / Use This Project

1. Install **Power BI Desktop** (free, from the Microsoft Store or [powerbi.microsoft.com](https://powerbi.microsoft.com/desktop/)).
2. Clone or download this repository.
3. Open `power-bi-file/HR_Employee_Details_Analysis.pbix`.
4. If prompted, point the data source to `original-data/hr-data.xlsx` on your local machine (Home → Transform data → Data source settings → Change Source).
5. Click **Refresh** on the Home ribbon to load the latest data.
6. Interact with the slicers and visuals to explore the KPIs.

---

## How This Repository Was Created (Git & GitHub Steps)

For reference/reproducibility, here is the exact workflow used to publish this project:

1. **Create the repository on GitHub**
   - Go to [github.com](https://github.com) → click **New repository**.
   - Name it, e.g., `HR-Employee-Details-Dashboard`.
   - Add a short description ("Power BI HR analytics dashboard – India vs New Zealand").
   - Choose **Public**, initialize with a `README.md`, and (optionally) a `.gitignore` for Power BI (`*.pbix.bak`) — click **Create repository**.

2. **Clone it locally**
   ```bash
   git clone https://github.com/<your-username>/HR-Employee-Details-Dashboard.git
   cd HR-Employee-Details-Dashboard
   ```

3. **Add the project folders/files**
   ```bash
   mkdir original-data data-model power-bi-file screenshots
   # copy hr-data.xlsx into original-data/
   # copy conceptual-model.png and physical-data-model.png into data-model/
   # copy the .pbix file into power-bi-file/
   # copy a dashboard screenshot into screenshots/
   ```

4. **Add the README and Documentation files** this file, and `[Documentation.md](https://github.com/LikithReddy128/-HR-Employee-Details-Analysis-Dashboard/blob/main/Documentation%20(1).md) to the repository root.

5. **Stage, commit, and push**
   ```bash
   git add .
   git commit -m "Add HR employee dashboard: data, model, Power BI file, and documentation"
   git push origin main
   ```

6. **Verify on GitHub** that all six items render correctly: raw data, both model images, the `.pbix` file (GitHub will show it as a downloadable binary), and both markdown files.

7. **(Optional) Add topics/tags** on the repo page — e.g. `power-bi`, `data-analytics`, `hr-analytics`, `dax`, `dashboard` — to make the project discoverable.

---

## Insights & Outcomes

- India (92 employees) and New Zealand (91 employees) have almost identical headcounts, making them a natural apples-to-apples comparison.
- Both countries show the same maximum salary ceiling (119K), suggesting a shared global pay band at the top end.
- Department mix is closely matched between the two countries — Website and Procurement are consistently the largest departments (~29–30% of headcount each), while HR is the smallest (~4%).
- Custom-sorting the Rating field (via the `field order` table) was necessary because Power BI's default alphabetical sort ("Above average, Average, Exceptional, Poor, Very poor") does not reflect the true performance scale.
- A dedicated `First Letter` calculated column turned a simple text slicer into a fast "search by name" tool for HR users browsing 183 records.

---

## Tech Stack
- **Power BI Desktop** – data modeling, DAX, visualization
- **Excel** – source data (`hr-data.xlsx`)
- **DAX** – Head Count, Max/Min/Average Salary measures
- **Git & GitHub** – version control and project publishing

---

## Author
Maintained as part of an HR analytics portfolio project. Feel free to fork this repository and adapt the model/measures to your own HR dataset.
