# Insurance Claims & Portfolio Risk Analytics

**Data Analysis & Business Intelligence — Python + Tableau**

## 1. Business Problem

This project analyzes a motor-insurance portfolio to identify where claims occur most frequently, where claim costs are highest, which driver and vehicle segments show different claim patterns, and which areas deserve closer management attention.

The project is designed as a **descriptive analytics and business intelligence** exercise. The analysis is intended to support management discussion and monitoring; it is not a pricing or underwriting model, and observed associations are not interpreted as causal relationships.

## 2. Dataset

The project uses the public **French Motor Third-Party Liability (freMTPL2)** dataset from CASdatasets.

The dataset contains two related tables:

- `freMTPL2freq`: policy-level information, exposure, risk characteristics, and claim counts.
- `freMTPL2sev`: claim-level information containing claim amounts linked to policies through `IDpol`.

The frequency table contains **677,991 policies**, while the severity table contains **26,444 observed claim records**.

### Official source

- [CASdatasets — freMTPL2 documentation](https://dutangc.github.io/CASdatasets/reference/freMTPL.html)
- [CASdatasets package index](https://dutangc.github.io/CASdatasets/reference/index.html)

### Important limitation

The freMTPL2 dataset does **not contain written premium**. Therefore, Loss Ratio is not calculated in this project.

## 3. Data Dictionary

### Policy / Frequency Table

| Field | Description |
|---|---|
| `IDpol` | Unique policy identifier |
| `ClaimNb` | Number of claims during the exposure period |
| `Exposure` | Policy exposure period in years |
| `VehPower` | Vehicle power |
| `VehAge` | Vehicle age |
| `DrivAge` | Driver age |
| `BonusMalus` | Bonus/Malus score |
| `VehBrand` | Vehicle brand category |
| `VehGas` | Fuel type |
| `Area` | Area category |
| `Density` | Population density |
| `Region` | Policy region |

### Severity Table

| Field | Description |
|---|---|
| `IDpol` | Policy identifier linking claims to policies |
| `ClaimAmount` | Observed claim cost |

## 4. Data Preparation and Cleaning

The analysis was performed in Python using pandas and NumPy.

### Main preparation steps

1. Loaded the frequency and severity tables.
2. Inspected shapes, columns, data types, descriptive statistics, missing values, and duplicate records.
3. Validated important numerical fields such as exposure, claim counts, and claim amounts.
4. Checked repeated `IDpol` values in the severity table. Repeated policy IDs are expected because a policy can have multiple claims.
5. Aggregated claim-level severity data to policy level using `IDpol`.
6. Calculated:
   - Total claim cost per policy
   - Number of claim records per policy
   - Average claim amount per policy
7. Merged the aggregated severity table with the frequency table using a left join on `IDpol`.
8. Preserved all policies, including policies with no observed claims.
9. Validated that the number of policies remained unchanged after the merge.
10. Validated that `ClaimNb` matched the number of claim records for each policy.
11. Created driver and vehicle segments for dashboard analysis.

### Claim handling

Policies without observed claims receive:

- `TotalClaimCost = 0`
- `ClaimRecords = 0`

Average claim severity is not interpreted as zero for policies without claims, because severity is meaningful only for observed claims.

Large claims were **not automatically removed**. They were reviewed as potential high-cost or unusual observations.

## 5. Segment Definitions

### Driver Age

- `<25`
- `25-39`
- `40-59`
- `60+`

### Vehicle Age

- `0-4`
- `5-9`
- `10-19`
- `20+`

### Vehicle Power

- `Low`
- `Medium`
- `High`
- `Very High`

### Bonus-Malus

- `50`
- `51-69`
- `70-99`
- `100+`

Fuel type and Region were also analyzed directly.

## 6. Core KPI Definitions

### Policy Count

Number of unique policies.

### Total Exposure

Sum of policy exposure in years.

### Claim Count

Total number of claims.

### Claim Frequency

```text
Claim Frequency = Claim Count / Total Exposure
```

This normalizes claim occurrence by exposure and makes groups with different exposure levels more comparable.

### Total Claim Cost

Sum of observed claim amounts.

### Average Claim Severity

Average cost of observed claims:

```text
Average Claim Severity = Total Claim Cost / Claim Count
```

Loss Ratio is not calculated because premium information is not available.

## 7. Portfolio Overview

The overall portfolio results are:

| KPI | Result |
|---|---:|
| Policy Count | 677,991 |
| Total Exposure | 358,482.84 years |
| Claim Count | 26,444 |
| Claim Frequency | 7.38% |
| Total Claim Cost | €59.91M |
| Average Claim Severity | €2,265.51 |

## 8. Key Findings

### 8.1 Driver Age

Drivers under 25 show a substantially higher observed claim frequency and severity:

- Claim Frequency: **16.10%**
- Average Severity: **€5,838.56**

This is a descriptive association and should not be interpreted as proof that age causes higher losses.

### 8.2 Bonus-Malus

The `100+` Bonus-Malus segment shows the strongest observed claim pattern:

- Claim Frequency: **26.25%**
- Average Severity: **€4,948.36**
- Policies: **27,324**

This makes the segment an important candidate for management monitoring.

### 8.3 Vehicle Power

Claim frequency increases across the vehicle-power groups:

- Low: **7.11%**
- Medium: **7.41%**
- High: **7.64%**
- Very High: **8.22%**

The High-power segment also has the highest observed average severity among the power groups at approximately **€2,957.63**.

### 8.4 Fuel Type

The two fuel groups show different frequency and severity patterns:

- Diesel: Frequency **7.88%**, Severity **€2,051.65**
- Regular: Frequency **6.92%**, Severity **€2,486.88**

This demonstrates that claim frequency and claim severity should be analyzed separately.

### 8.5 Regional Patterns

- **Rhone-Alpes** has the highest regional claim frequency at approximately **9.34%**.
- **Centre** has the highest total claim cost at approximately **€19.07M**.
- **Champagne-Ardenne** has very high observed severity at approximately **€6,249.98**, but only 77 observed claims, so this result should be interpreted cautiously and investigated rather than treated as a definitive ranking.

### 8.6 Claim Cost Concentration

The Pareto analysis shows strong concentration of claim costs:

- Top 1% of claims → **37.99%** of total claim cost
- Top 5% of claims → **52.09%**
- Top 10% of claims → **59.92%**

This indicates that a relatively small number of high-cost claims account for a large share of total claim expenditure.

### 8.7 High-Cost / Unusual Claims

The largest observed claim is approximately **€4.08M**.

The IQR-based review identified **3,842 potential high-cost outliers** above the calculated upper bound. These observations were retained rather than automatically removed, because an extreme insurance claim may represent a genuine business event.

## 9. Tableau Dashboard Structure

The Tableau workbook contains three main dashboard views.

### Dashboard 1 — Executive Portfolio Overview

Includes:

- Policy Count
- Exposure
- Claim Count
- Claim Frequency
- Total Claim Cost
- Average Claim Severity
- Regional claim frequency comparison
- Regional claim cost comparison
- Interactive filters

Purpose:

Provide a quick view of overall portfolio size, claim frequency, and claim cost.

### Dashboard 2 — Claims & Risk Segments

Includes:

- Driver Age analysis
- Bonus-Malus analysis
- Vehicle Power analysis
- Fuel analysis
- Frequency vs Severity by Region
- Interactive filters for important segments

Purpose:

Identify segments with different claim frequency and severity patterns.

### Dashboard 3 — Claim Cost Concentration

Includes:

- Pareto claim-cost analysis
- Top 10 highest-cost claims
- Regional claim cost comparison
- Bonus-Malus claim cost comparison
- Key findings
- Three management recommendations

Purpose:

Show concentration of claim costs and highlight areas for management review.

## 10. Data-Driven Recommendations

### Recommendation 1 — Monitor the Bonus-Malus 100+ Segment

**Evidence:** The 100+ segment has a 26.25% observed claim frequency and approximately €4,948 average severity.

**Action:** Prioritize this segment for regular claims monitoring and detailed portfolio review.

**KPI:** Claim Frequency and Average Claim Severity.

### Recommendation 2 — Monitor Drivers Under 25

**Evidence:** Drivers under 25 show a 16.10% observed claim frequency and approximately €5,839 average severity.

**Action:** Track this segment separately in recurring claims and portfolio reviews.

**KPI:** Claim Frequency and Average Claim Severity.

### Recommendation 3 — Focus on High-Cost Claims

**Evidence:** The top 1% of claims account for approximately 37.99% of total claim cost.

**Action:** Maintain focused monitoring of high-cost claims and track their contribution to total claim expenditure.

**KPI:** Cumulative Claim Cost Share.

## 11. Limitations

- The dataset is historical and describes an anonymous insurer.
- The analysis is descriptive and does not establish causation.
- Premium information is unavailable, so Loss Ratio cannot be calculated.
- The available variables do not capture every factor that may influence insurance risk.
- Small groups, such as regions with relatively few claims, should be interpreted cautiously.
- High-cost observations were retained and flagged for review rather than automatically removed.

## 12. Repository Structure

```text
insurance-claims-dashboard/
│
├── README.md
├── requirements.txt
│
├── notebooks/
│   └── insurance_analysis.ipynb
│
├── data/
│   ├── raw/
│   │   ├── freMTPL2freq.csv
│   │   └── freMTPL2sev.csv
│   │
│   └── processed/
│       ├── tableau_master.csv
│       ├── tableau_region_summary.csv
│       ├── tableau_driver_age_summary.csv
│       ├── tableau_vehicle_age_summary.csv
│       ├── tableau_vehicle_power_summary.csv
│       ├── tableau_fuel_summary.csv
│       ├── tableau_bonus_malus_summary.csv
│       ├── tableau_claim_pareto.csv
│       └── tableau_top_10_claims.csv
│
├── tableau/
│   └── insurance_claims_dashboard.twbx
│
├── figures/
│   ├── dashboard_1_overview.png
│   ├── dashboard_2_segments.png
│   └── dashboard_3_cost_concentration.png
│
└── report/
    └── business_summary.md
```

## 13. Reproducibility

### Step 1 — Install Python dependencies

```bash
pip install -r requirements.txt
```

### Step 2 — Place the raw dataset

Put `freMTPL2freq.csv` and `freMTPL2sev.csv` in:

```text
data/raw/
```

### Step 3 — Run the notebook

Open:

```text
notebooks/insurance_analysis.ipynb
```

and run the notebook from beginning to end.

The notebook performs the complete workflow:

```text
Load
→ Inspect
→ Validate / Clean
→ Aggregate Claims
→ Merge
→ Create Segments
→ Calculate KPIs
→ Regional Analysis
→ Frequency vs Severity
→ Pareto Analysis
→ Outlier Review
→ Export Tableau-ready CSVs
```

### Step 4 — Update Tableau

Open the Tableau workbook and refresh each data source connected to the processed CSV files.

### Step 5 — Review the final dashboards

Check all three dashboards and confirm that the displayed values correspond to the latest exported CSV files.

## 14. Project Outcome

The final project combines Python-based insurance data preparation and analysis with Tableau-based business intelligence.

The main outcome is an executive dashboard that helps management understand:

- portfolio size,
- claim frequency,
- claim severity,
- regional differences,
- segment-level patterns,
- concentration of high-cost claims,
- and areas that deserve ongoing monitoring.



# Author

**Maliheh Abbasi**

