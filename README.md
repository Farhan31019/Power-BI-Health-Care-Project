# 🏥 Health Care Dashboard (Power BI)

An interactive Power BI dashboard that analyses **5,000 hospital visits (Jan 2024 – May 2025)** to show where healthcare money goes, who covers it, and how much patients end up paying out of pocket.

![Dashboard preview](images/dashboard-preview.png)
<!-- Add a screenshot of the dashboard at images/dashboard-preview.png -->

---

## 📌 Project Overview

Hospital billing is hard to read when it is spread across visits, departments, procedures and insurers. This project models the data as a star schema and presents it on a single-page dashboard, so a viewer can quickly answer questions like:

- What is the total billing, and how much does insurance cover?
- Which departments, procedures and diagnoses drive the most cost?
- How do Inpatient, Outpatient and Emergency visits compare?
- Which cities generate the most billing?
- How much do patients pay out of pocket, and in which departments?

## 📊 Key Findings

| Metric | Result |
|---|---|
| Total billing | ~3.36M |
| Insurance coverage | ~2.23M |
| Out-of-pocket (patient-paid) | ~1.13M |
| Average billing per visit | ~671 |
| Top departments | Cardiology (25%), Orthopedics (24%), General Surgery (23%) |
| Top procedure | X-Ray (~31% of billing), followed by CT Scan (~24%) |
| Top diagnosis | Hypertension (~40% of billing) |
| Service type | Outpatient visits account for about half of total billing |

> Percentages are shares of total billing. Figures are calculated from the dataset using the measures below.

## 🧩 Dashboard Contents

| Visual | Purpose |
|---|---|
| KPI cards | Billing, insurance, out-of-pocket, treatment, medication and room costs, with averages |
| Column chart | Billing amount by procedure |
| Clustered bar chart | Billing amount by department |
| Funnel chart | Out-of-pocket cost by department |
| 100% stacked bar chart | Billing by diagnosis, split by service type (Inpatient / Outpatient / Emergency) |
| Map | Treatment cost by city |
| Slicers | Year, cost category, and location (city/state) |

The cost-category and location slicers are built with **field parameters**, so the same visuals can switch between measures and dimensions without separate pages.

## 🗂️ Data Model

The model is a **star schema** with one fact table and several dimension tables.

```
                 Patient ── CITY
                    │
Provider ──┐        │
Department ┤        │
Diagnosis ─┼──── Fact Visit ──── Date
Procedure ─┤
Insurance ─┘
```

| Table | Type | Description |
|---|---|---|
| `Fact Visit` | Fact | One row per visit: dates, service type, treatment and medication cost, insurance coverage, room type, daily room rate, length of stay, payment status, satisfaction score, referral source |
| `Patient` | Dimension | Patient ID, gender, age, race, city |
| `Provider` | Dimension | Provider name, gender, nationality, age |
| `Department` | Dimension | Cardiology, General Surgery, Neurology, Orthopedics, Pediatrics |
| `Diagnosis` | Dimension | Appendicitis, Asthma, Fracture, Hypertension, Migraine |
| `Procedure` | Dimension | Blood Test, CT Scan, MRI Scan, Ultrasound, X-Ray |
| `Insurance` | Dimension | Aviva, AXA, Allianz |
| `CITY` | Dimension | City and state/region |
| `Date` | Dimension | Year, month, quarter |
| `All Measures` | Measure table | Holds all DAX measures |

## 🧮 Core DAX Measures

```DAX
Total Treatment Cost   = SUM ( 'Fact Visit'[Treatment Cost] )
Total Medication Costs = SUM ( 'Fact Visit'[Medication Cost] )
Total Insurance        = SUM ( 'Fact Visit'[Insurance Coverage] )

Total Room Charges =
SUMX (
    'Fact Visit',
    'Fact Visit'[Room Charges(daily rate)] * 'Fact Visit'[Total admission date]
)

Billing Amount =
[Total Medication costs] + [Total Room Charges] + [Total Treatment Cost]

Out of Pocket = [Billing Amount] - [Total Insurance]

Average Billing =
AVERAGEX (
    'Fact Visit',
    'Fact Visit'[Treatment Cost]
        + 'Fact Visit'[Medication Cost]
        + 'Fact Visit'[Room Charges(daily rate)] * 'Fact Visit'[Total admission date]
)
```

Other measures: `Average Treatment`, `Average Medication`, `Average Room Charges`, `Average Insurance`, `Procedure %` and `Department %`.

## 🛠️ Tools & Skills

- **Power BI Desktop**
- **DAX** (measures, iterators, percentage-of-total logic)
- **Data modelling** (star schema, relationships, date table)
- **Field parameters** for dynamic slicers
- **Data visualization** and dashboard layout (1920×1080)

## 🚀 How to Use

1. Clone or download this repository.
2. Open `Health_Care_Dash_Board.pbix` in [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
3. Use the slicers (year, cost category, location) to filter the page, and click any visual to cross-highlight the others.

## 📁 Repository Structure

```
├── Health_Care_Dash_Board.pbix   # Power BI report and data model
├── images/
│   └── dashboard-preview.png     # Dashboard screenshot
└── README.md
```

## 📝 Notes

- The dataset appears to be **synthetic / for practice**. Findings illustrate the analysis approach and should not be read as real-world healthcare statistics.
- Billing Amount is calculated as treatment + medication + (daily room rate × days admitted). Out-of-pocket is billing minus insurance coverage.
- Currency is not specified in the source data, so values are shown without a currency symbol.

## 🔮 Future Improvements

- Add patient satisfaction and follow-up visit analysis
- Add a payment status view (Paid vs Pending)
- Add trend analysis by month and quarter
- Add provider performance and referral source pages

## 📬 Connect

Built by **Farhan**. Feedback and suggestions are welcome. Feel free to open an issue or connect on LinkedIn.
