# Insurance Claims & Portfolio Risk Analysis

## Project Overview

This project analyzes the French Motor Third-Party Liability (MTPL) insurance dataset using Python and Power BI.

The main goal is to identify claim frequency, claim severity, high-cost claims, and high-risk segments.

## Dataset

The dataset is the French Motor Third-Party Liability (MTPL) dataset (freMTPL2).

It contains two main files:

* freMTPL2freq.csv
* freMTPL2sev.csv

Official dataset source:

https://dutangc.github.io/CASdatasets/reference/freMTPL.html

## Main Variables

The main variables used in the analysis are:

* IDpol: Policy ID
* ClaimNb: Number of claims
* Exposure: Exposure period
* VehPower: Vehicle power
* VehAge: Vehicle age
* DrivAge: Driver age
* BonusMalus: Bonus-Malus coefficient
* VehBrand: Vehicle brand
* VehGas: Fuel type
* Area: Geographic area
* Density: Population density
* Region: Region
* ClaimAmount: Claim amount

The dataset does not contain premium information, so Loss Ratio was not calculated.

## Data Preparation

The frequency and severity datasets were checked and prepared using Python.

The two datasets were linked using IDpol.

The severity data was aggregated at policy level and then merged with the frequency data.

Duplicate claim records were also checked during data preparation.

High-value claims were not automatically removed because they are important for insurance risk analysis.

## Main KPIs

Policy Count: 677,991

Exposure: 358,482.84

Claim Count: 26,444

Total Claim Cost: 59,909,216.50

Claim Frequency: 0.07377

Average Claim Severity: 2,265.51

Claim Frequency = Claim Count / Exposure

Average Claim Severity = Total Claim Cost / Claim Count

## Python Analysis

Python was used for:

* Data loading
* Data cleaning
* Duplicate checking
* Data merging
* Data aggregation
* Creating driver age groups
* Creating vehicle age groups
* Creating Bonus-Malus groups
* Claim frequency analysis
* Claim severity analysis
* High-risk segment analysis
* High-cost claim analysis
* Preparing data for the dashboard

The Python notebook is:

insurance_analysis.ipynb

## Power BI Dashboard

The original project requested Tableau.

Because Tableau was not available for this implementation, Power BI was used to create the final dashboard.

The Power BI file is:

Insurance_Claims_Portfolio_Risk_Dashboard.pbix

The dashboard has three pages.

### Page 1: Executive Portfolio Overview

This page contains:

* Policy Count
* Exposure
* Claim Count
* Claim Frequency
* Total Claim Cost
* Average Claim Severity
* Regional comparisons
* Filters

### Page 2: Claims & Risk Segments

This page analyzes:

* Driver age groups
* Bonus-Malus groups
* Vehicle age groups
* Claim frequency
* Average claim severity

The page includes a scatter plot comparing claim frequency and average claim severity by Bonus-Malus group.

### Page 3: Claim Cost Concentration & Risk Analysis

This page contains:

* Pareto analysis of claim cost
* Outlier claim cost by driver age group
* Driver age filter
* Three business recommendations

## Key Findings

1. Drivers aged 18–25 show a relatively high-frequency and high-severity pattern.

2. The Bonus-Malus 91–100 group has both high claim frequency and high average claim severity.

3. Claim frequency generally decreases across the older vehicle-age groups.

4. High-cost claims represent a large share of total claim cost.

5. The IQR outlier threshold for claim amount was approximately 2,241.32.

6. There were 3,761 identified outlier claim records, representing approximately 65.6% of total claim cost.

## Business Recommendations

### Recommendation 1

Evidence: High-cost claims account for a large share of total claim cost.

Action: Give high-cost claims more detailed review and claims-management attention.

KPI: Cumulative Claim Cost % and Outlier Claim Cost Share.

### Recommendation 2

Evidence: Bonus-Malus 91–100 has high claim frequency and high average severity.

Action: Monitor this segment more closely.

KPI: Claim Frequency and Average Claim Severity.

### Recommendation 3

Evidence: Drivers aged 18–25 show a relatively high-frequency and high-severity pattern.

Action: Monitor claims performance and high-cost claims for this age group.

KPI: Claim Frequency, Average Claim Severity, and Claim Cost Share.

## Limitations

* The dataset does not contain premium information.
* Loss Ratio therefore cannot be calculated.
* Observed relationships do not necessarily mean causation.
* The dataset represents French MTPL insurance.
* Claim frequency and claim severity should be interpreted separately.
* High-value claims were retained in the analysis.

## Project Files

The main project files are:

* README.md
* requirements.txt
* insurance_analysis.ipynb
* Cleaned CSV files
* Insurance_Claims_Portfolio_Risk_Dashboard.pbix
* Dashboard screenshots

## Requirements

The Python packages used in the project are listed in requirements.txt.

Main packages:

* pandas
* numpy
* matplotlib
* jupyter

## Reproduction

To reproduce the analysis:

1. Install Python.
2. Install the packages in requirements.txt.
3. Open insurance_analysis.ipynb.
4. Run the notebook cells.
5. Use the cleaned data for the Power BI dashboard.
6. Open the PBIX file to review the final dashboard.
