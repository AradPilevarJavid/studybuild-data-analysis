# Insurance Claims & Portfolio Risk

## Business Summary

### Objective

The purpose of this analysis is to understand where claim activity and claim costs are concentrated across a motor insurance portfolio and to identify patterns that may deserve closer management attention.

The analysis is descriptive and focuses on portfolio monitoring rather than pricing or underwriting decisions.

---

## What the Data Shows

### 1. Claim rate differs substantially across Bonus-Malus groups

The observed claim rates increase noticeably across the higher Bonus-Malus groups.

The 50 Bonus-Malus group has a claim rate of approximately **2.88%**, compared with approximately **19.14%** for the 151+ group.

This is an important portfolio pattern, but it should be treated as an association in the historical data rather than evidence that Bonus-Malus itself causes higher claim frequency.

**Why it matters:**

Bonus-Malus groups can be useful dimensions for monitoring changes in claim activity and identifying segments that deserve further investigation.

---

### 2. Driver and vehicle segments show different claim patterns

Claim patterns vary across driver age, vehicle age, and other portfolio segments.

Drivers aged **18–25** show the highest observed claim frequency among the driver-age segments, at approximately **5.71%**. Claim frequency is also highest for vehicles aged **6–10**, at approximately **4.42%**, and does not increase consistently with vehicle age.

**Why it matters:**

Differences across these segments can help management identify areas for further portfolio review rather than relying on a single overall portfolio measure.

---

### 3. Claim cost is concentrated

The analysis of high-cost policies shows that claim cost is not evenly distributed across the portfolio.

A relatively small group of policies contributes a substantial share of total claim cost.

**Why it matters:**

Changes in overall claim cost may sometimes be driven by a limited number of expensive policies rather than a broad increase across the entire portfolio.

This makes cost concentration an important metric for claims management.

---

# Recommendations

## Recommendation 1 — Prioritize review of 151+ Bonus-Malus policies

**Evidence:**

The 151+ Bonus-Malus segment has an observed claim rate of **19.14%**, around **6.6×** the rate of the 50 segment.

**Action:**

Prioritize this segment for portfolio review and investigate whether the observed difference persists across other relevant characteristics.

**KPI:**

Claim Rate + Average Claim Severity.

---

## Recommendation 2 — Monitor the 18–25 driver segment

**Evidence:**

Drivers aged **18–25** show the highest observed claim frequency among the driver-age segments, at approximately **5.71%**.

**Action:**

Monitor this segment regularly and investigate whether the observed difference persists across vehicle and other portfolio characteristics.

**KPI:**

Claim Frequency + Average Claim Severity.

---

## Recommendation 3 — Strengthen large-loss monitoring

**Evidence:**

High-cost claims are concentrated in a limited number of policies and segments, meaning a relatively small group can contribute a substantial share of total claim cost.

**Action:**

Include high-cost policy concentration in regular claims-management reviews and investigate significant changes in the concentration of claim cost.

**KPI:**

High-Cost Claim Cost + High-Cost Cost Share.

---

# Management Takeaway

The main takeaway is that **claim frequency and claim cost are not the same story**.

A portfolio can have frequent claims without having the highest severity, while a relatively small number of expensive policies can have a large effect on total claim cost.

For this reason, a useful monitoring framework should bring together:

**Exposure → Frequency → Severity → Total Cost → Cost Concentration**

rather than relying on a single "risk" measure.

The Tableau dashboard was designed around this idea: to make the main portfolio patterns easy to compare and to provide a starting point for further business investigation.

