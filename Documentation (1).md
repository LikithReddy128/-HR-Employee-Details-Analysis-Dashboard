# Documentation – HR Employee Details Dashboard

This document provides the detailed project write-up referenced from the main `README.md`: the project aim, dataset description, data-cleaning process, modeling decisions, final results, and insights.

---

## 1. Project Aim / Goal

To design and build a Power BI dashboard that gives HR and leadership a fast, visual, self-service way to answer core workforce questions — headcount, diversity mix, age profile, pay bands, top performers/earners, hiring trends, and a direct India-vs-New Zealand comparison — replacing manual spreadsheet lookups with interactive filtering.

**Target audience:** HR Business Partners, People Analytics team, regional country managers (India & New Zealand).

**Success criteria:**
- All 10 analysis questions (see below) are answerable in under 3 clicks.
- The dashboard refreshes cleanly from the source Excel file.
- Slicers allow drilling into any single department, gender, rating band, age group, or name-starting-letter.

---

## 2. The Dataset Description

- **Source file:** `original-data/hr-data.xlsx`, sheet `Data`.
- **Grain:** one row per employee.
- **Row count:** 183 employees.
- **Columns (as received):**

| Column | Data type | Example | Notes |
|---|---|---|---|
| Name | Text | "Parasuramudu Jamakayala" | Unique per employee |
| Gender | Text | Male / Female / Other | 3 categories |
| Age | Whole number | 20–46 | |
| Rating | Text | Average, Above average, Exceptional, Poor, Very poor | 5 categories, no inherent alphabetical order |
| Date Joined | Date | 18-Oct-2020 | Range: 07-May-2020 to 29-Apr-2023 |
| Department | Text | Website, Procurement, Finance, Sales, HR | 5 categories |
| Salary | Whole number | 112,650 | Annual salary, single currency field |
| Country | Text | IND, NZ | 2 categories |

---

## 3. Data Cleaning Process

The following steps were carried out (in Excel / Power Query) before the data was loaded into the model:

1. **Header validation** – confirmed the first row held clean, single-word-style column headers (`Name`, `Gender`, `Age`, `Rating`, `Date Joined`, `Department`, `Salary`, `Country`) with no merged cells or blank header rows above them.
2. **Data type correction** – set `Age` and `Salary` to whole numbers, `Date Joined` to Date, and all remaining fields to Text, so Power BI does not misinterpret any column (e.g. Salary being read as text).
3. **Duplicate check** – checked `Name` for exact duplicates to make sure each of the 183 rows represents a distinct employee record.
4. **Blank/null check** – scanned all 8 columns for blank cells; rows with missing critical fields (Salary, Department, Country) would be flagged for review or removal.
5. **Category standardization** – trimmed whitespace and normalized casing on categorical text fields (`Gender`, `Department`, `Rating`, `Country`) so that, for example, "hr" and "HR" are not treated as two different departments.
6. **Country code consistency** – standardized the country field to two short codes, `IND` and `NZ`, instead of mixed full-name/abbreviation entries.
7. **Outlier sanity check** – reviewed `Age` (19–46) and `Salary` ranges for values outside plausible working-age/pay bounds.
8. **Calculated/support columns added in Power BI** (post-load, not in the raw file):
   - `First Letter = LEFT(Data[Name], 1)` — for the name-search slicer.
   - `Age (bins)` — grouped via Power BI's built-in "New group" binning on the `Age` column, to feed the histogram.
   - `field order` table — a small manual lookup table pairing each `Rating` value with a numeric `Order` (1 = Very poor, 2 = Poor, 3 = Average, 4 = Above average, 5 = Exceptional), related to `Data[Rating]` and used with **Sort by Column** so Rating displays logically rather than alphabetically.

---

## 4. Data Model Summary

- **Model type:** single fact-style table (`Data`) plus one small lookup table (`field order`).
- **Relationship:** `field order[Rating]` → `Data[Rating]`, one-to-many, used only to carry the sort order (not used as a filtering relationship).
- **Measures (DAX):**
  ```
  Head Count      = COUNTROWS(Data)
  Max Salary      = MAX(Data[Salary])
  Min Salary      = MIN(Data[Salary])
  Average Salary  = AVERAGE(Data[Salary])
  ```
- See `data-model/conceptual-model.png` and `data-model/physical-data-model.png` for the visual diagrams, and the main `README.md` for the full column-by-column model table.

---

## 5. Analysis Questions → Dashboard Mapping

| # | Question | Visual | Field(s) / Slicer |
|---|---|---|---|
| 1 | How many people are in each department? | Bar chart | Department (axis), Head Count (value) |
| 2 | Gender distribution by department? | Clustered/stacked bar | Department, Gender, Head Count |
| 3 | Age spread of staff? | Histogram | Age (bins), Head Count |
| 4 | Min/Max/Average salary per department? | Table | Department, Min Salary, Max Salary, Average Salary |
| 5 | Top earners in each country? | Sorted table | Country slicer, Name, Salary (sorted desc) |
| 6 | Performance spread (sorted meaningfully)? | Bar chart | Rating (sorted by `field order[Order]`), Head Count |
| 7 | Company growth trend? | Line chart | Date Joined (by Year), Head Count (running count) |
| 8 | Employee filter by starting letter? | Slicer | First Letter |
| 9 | Performance vs. Salary relationship? | Scatter chart | Rating / Order (x), Salary (y) |
| 10 | India vs. New Zealand scorecard? | Two themed pages / side-by-side cards | Country, Head Count, Max Salary, Department donut, Salary table |

---

## 6. Final Result

A two-page (India / New Zealand) Power BI dashboard, each page carrying:
- A **Head Count** card
- A **Max Salary** card
- A **Head Count by Department** donut chart
- A **Min/Max Salary by Department** table

...plus supporting pages/visuals for gender mix, age histogram, top earners, sorted performance spread, hiring trend line, and a name-search slicer — together covering all 10 analysis themes with 6 interactive slicers (Country, Department, Gender, Rating, Age bins, First Letter).

**Headline numbers observed:**
- India: 92 employees, 119K max salary.
- New Zealand: 91 employees, 119K max salary.
- Department split is near-identical between the two countries: Website and Procurement are the two largest departments (~29–30% each), HR the smallest (~4%).

---

## 7. Outcomes

- A single `.pbix` file now replaces manual Excel filtering/pivoting for HR headcount and pay questions.
- Country managers can self-serve the India-vs-New Zealand comparison instead of requesting a custom report each time.
- The custom Rating sort order (via `field order`) makes the performance-spread chart interpretable at a glance, instead of showing categories in confusing alphabetical order.
- The `First Letter` slicer gives a lightweight way to locate a specific employee in a 183-row dataset without a full search box.

---

## 8. Insights

- Headcount and top-line pay (max salary) are almost perfectly mirrored between India and New Zealand, suggesting the two offices are intentionally staffed and compensated at a similar scale.
- Department distribution patterns (Website/Procurement largest, HR smallest) hold across both countries, indicating a consistent global org structure rather than region-specific staffing choices.
- Because `Rating` and `Salary` are both stored per employee, the scatter chart (Q9) lets HR visually sanity-check whether higher performance ratings correlate with higher pay — useful input for compensation-review conversations.
- The 2020–2023 `Date Joined` range makes the growth-trend line chart a useful lens for seeing which years had the heaviest hiring, which can be cross-referenced with department growth.

---

## 9. Limitations / Next Steps

- Salary currency is assumed to be local (INR for India, NZD for New Zealand) but is not explicitly labeled in the source file — a currency column would remove ambiguity.
- The dataset has no employee-tenure/exit field, so attrition cannot currently be analyzed — only headcount and hiring trend.
- Adding a `Manager` or `Job Level` column in a future data refresh would allow org-hierarchy and pay-equity-by-level analysis.
