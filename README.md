# HR Attrition & Overtime Dashboard (Power BI)

A one-page Power BI dashboard (Arabic interface) that looks at how **overtime** relates to **employee attrition**, broken down by job satisfaction, work-life balance and job level.

📁 **Open:** `dash board.pbip` in Power BI Desktop. This is a Power BI Project (PBIP), so the model (TMDL) and report (PBIR) are stored as readable text files.

---

## Headline numbers

| Metric | Value |
|---|---|
| Employees | 1,474 |
| Attritions | 237 |
| Overall attrition rate | 16.1% |
| Overtime multiplier | **2.93×** |

---

## Key insights

1. **Overtime roughly triples attrition:** employees who work overtime leave at **30.5%**, compared with **10.4%** for those who don't.
2. **The overtime gap holds at every satisfaction level:** even among highly satisfied employees, attrition is 21.1% with overtime vs 6.9% without.
3. **Work-life balance matters most when combined with overtime:** employees with *bad* work-life balance who also work overtime have the highest attrition rate in the dataset (**45.5%**).
4. **Entry-level employees leave most:** job level 1 accounts for most attritions (143 of 237), and overtime makes it worse (82 with overtime vs 61 without).

---

## Dashboard layout

- **Header** with the title and a one-line description.
- **KPI cards:** attrition rate, total employees, total attritions, and the overtime multiplier (highlighted).
- **Overtime slicer** (Yes / No) that filters every visual on the page.
- **Four charts:**
  - Attrition rate by overtime
  - Attrition rate by job satisfaction × overtime
  - Attrition rate by work-life balance × overtime
  - Attrition count by job level × overtime

Overtime uses the same colors throughout: orange = works overtime, blue = no overtime.

---

## Data model

Four aggregated tables loaded from an Excel workbook:

| Table | Grain |
|---|---|
| `Overtime_Attrition` | Overtime (Yes / No): attrition count, employees, rate |
| `Satisfaction_Attrition` | Job satisfaction × overtime: attrition rate |
| `WorkLife_Attrition` | Work-life balance × overtime: attrition rate |
| `JobLevel_Attrition` | Job level × overtime: attrition count, average income |

`Overtime_Attrition` acts as the overtime dimension. The other three tables relate to it on `Overtime` (many-to-one), so a single slicer filters the whole page.

### DAX measures

```DAX
-- Total attritions
SUM ( Overtime_Attrition[AttritionCount] )

-- Overall attrition rate
DIVIDE ( [Total Attritions], SUM ( Overtime_Attrition[Total Employees] ) )

-- Overtime multiplier: attrition rate with overtime ÷ without
DIVIDE (
    CALCULATE ( SELECTEDVALUE ( Overtime_Attrition[AttritionRate] ), Overtime_Attrition[Overtime] = "Yes" ),
    CALCULATE ( SELECTEDVALUE ( Overtime_Attrition[AttritionRate] ), Overtime_Attrition[Overtime] = "No" )
)
```

(Names are translated here; in the model, the measures and some columns have Arabic names.)

### Calculated columns

The numeric survey scores are turned into readable labels and sorted by the original code (Sort by Column):

```DAX
Satisfaction Level =
SWITCH ( Satisfaction_Attrition[JobSatisfaction],
    1, "Low", 2, "Medium", 3, "High", 4, "Very High" )
```

The same approach is used for work-life balance (Bad / Good / Better / Best).

---

## Note on the data source

The model reads from a local Excel file (`HR_Attrition_PowerBI_Data.xlsx`). The project includes the cached data, so it opens and displays as-is. To refresh, point the Power Query source to your own copy of the workbook.

---

## Tools

Power BI Desktop · Power Query · DAX · PBIP (TMDL / PBIR)

## Author

**Nawaf**, Data Analyst
