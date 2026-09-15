# Motor Insurance Portfolio Analysis (French MTPL2) — README


# 🇮🇷 فارسی

## فهرست مطالب
1. معرفی پروژه
2. فایل‌های پروژه
3. دیکشنری کامل داده (Data Dictionary) با آمار واقعی
4. خلاصه‌ی یافته‌های EDA (مستقیماً از نوت‌بوک)
5. معماری Merge و تجمیع داده
6. نکته‌ی حیاتی کیفیت داده
7. نقشه‌ی کامل سلول‌های نوت‌بوک
8. متغیرهای کلیدی
9. خلاصه‌ی یافته‌ی هر سؤال (Q1-Q8)
10. سه توصیه‌ی نهایی
11. خلاصه‌ی روش‌شناسی
12. نحوه‌ی اجرا
13. بسته‌ی Power BI
14. محدودیت‌ها
15. واژه‌نامه

---

## ۱. معرفی پروژه

این پروژه یک تحلیل داده‌محور و آموزشی روی دیتاست عمومی و معروف **French MTPL2** (بیمه‌ی شخص ثالث خودرو) است. هدف: بررسی سلامت کلی یک پرتفوی بیمه، شناسایی سگمنت‌های پرریسک، اندازه‌گیری تمرکز هزینه‌ی خسارت (Tail Risk)، تعریف چارچوب پایش ماهانه، و ارائه‌ی سه توصیه‌ی کسب‌وکاری — از طریق پاسخ به ۸ سؤال تحلیلی.

```
Claim Frequency        = Total Claims / Total Exposure
Average Claim Severity = Total Claim Cost / Number of Claims
Pure Premium            = Claim Frequency × Average Claim Severity
```


## ۳. دیکشنری کامل داده (با آمار واقعی خروجی `describe()`)

### فایل `freMTPL2freq.csv` — ۶۷۸,۰۱۳ ردیف × ۱۲ ستون

| ستون | نوع | میانگین | میانه | Min | Max | توضیح |
|---|---|---|---|---|---|---|
| `IDpol` | float64 | — | — | ۱ | ۶,۱۱۴,۳۳۰ | شناسه‌ی یکتای بیمه‌نامه (کلید Join) |
| `ClaimNb` | int64 | ۰.۰۵۳ | ۰ | ۰ | ۱۶ | تعداد خسارت ثبت‌شده برای بیمه‌نامه |
| `Exposure` | float64 | ۰.۵۲۹ | ۰.۴۹ | ۰.۰۰۲۷ | ۲.۰۱ | مدت پوشش بیمه‌ای به سال |
| `VehPower` | int64 | ۶.۴۵ | ۶ | ۴ | ۱۵ | قدرت موتور خودرو |
| `VehAge` | int64 | ۷.۰۴ | ۶ | ۰ | ۱۰۰ | سن خودرو (سال) |
| `DrivAge` | int64 | ۴۵.۵ | ۴۴ | ۱۸ | ۱۰۰ | سن راننده |
| `BonusMalus` | int64 | ۵۹.۷۶ | ۵۰ | ۵۰ | ۲۳۰ | ضریب تخفیف/جریمه (۵۰ = حداکثر تخفیف) |
| `VehBrand` | string | — | — | — | — | برند خودرو (رمزگذاری‌شده، مثل B1، B11...) |
| `VehGas` | string | — | — | — | — | نوع سوخت (Diesel/Regular) |
| `Area` | string | — | — | — | — | کد منطقه‌ی سکونت |
| `Density` | int64 | ۱,۷۹۲ | ۳۹۳ | ۱ | ۲۷,۰۰۰ | تراکم جمعیتی منطقه‌ی سکونت |
| `Region` | string | — | — | — | — | نام منطقه‌ی جغرافیایی (فرانسه) |

### فایل `freMTPL2sev.csv` — ۲۶,۴۴۴ ردیف × ۲ ستون

| ستون | نوع | میانگین | میانه | Min | Max | توضیح |
|---|---|---|---|---|---|---|
| `IDpol` | int64 | — | — | ۱۳۹ | ۶,۱۱۳,۹۷۱ | شناسه‌ی بیمه‌نامه‌ی مرتبط با خسارت |
| `ClaimAmount` | float64 | ۲,۲۶۵.۵ | ۱,۱۷۲ | ۱ | ۴,۰۷۵,۴۰۱ | مبلغ خسارت پرداختی |

> نکته: بیشترین مبلغ خسارت ثبت‌شده (`۴,۰۷۵,۴۰۱`) نسبت به میانه (`۱,۱۷۲`) نزدیک به **۳,۵۰۰ برابر** بزرگ‌تر است — همان چولگی شدیدی که در سؤال ۵ با منحنی لورنز و ضریب جینی اندازه‌گیری شد.

## ۴. خلاصه‌ی یافته‌های EDA (مستقیماً برگرفته از نوت‌بوک)

- **`ClaimNb` به‌شدت Zero-Inflated است:** بیش از ۷۵٪ بیمه‌نامه‌ها صفر خسارت دارند (حتی صدک ۷۵ام هم صفر است)؛ این یعنی برای مدل‌سازی واقعی، به مدل‌های داده‌ی شمارشی (Poisson، Negative Binomial یا Tweedie) نیاز است، نه رگرسیون خطی ساده.
- **`Exposure` میانگینش تقریباً نیم سال (۰.۵۳) است** با صدک ۷۵ام برابر ۰.۹۹ (یعنی بیشتر بیمه‌نامه‌های کامل، یک‌ساله‌اند)؛ به همین دلیل Exposure باید به‌عنوان **Offset** در هر مدل فرکانس استفاده شود.
- **`DrivAge`** توزیع نسبتاً متعادلی بین ۱۸ تا ۱۰۰ سال دارد (میانگین ۴۵.۵).
- **`BonusMalus`** به‌شدت چوله است: بیش از نیمی از بیمه‌نامه‌ها دقیقاً روی مقدار پایه‌ی ۵۰ (حداکثر تخفیف) قرار دارند.
- **`VehAge`** میانه‌اش ۶ سال است، ولی حداکثر ۱۰۰ سال دیده می‌شود — این می‌تواند نشانه‌ی خودروی کلکسیونی یا خطای ثبت داده باشد و نیاز به بررسی اعتبارسنجی دارد.
- **`Density`** واریانس فوق‌العاده بالایی دارد (میانه=۳۹۳ در برابر میانگین=۱,۷۹۲)؛ نوت‌بوک تبدیل لگاریتمی (`np.log1p`) را برای این متغیر پیش از هر مدل‌سازی توصیه می‌کند.

## ۵. معماری Merge و تجمیع داده (دقیقاً طبق مستندسازی نوت‌بوک)

```python
sev_policy = sev.groupby("IDpol", as_index=False).agg(
    TotalClaimCost=("ClaimAmount", "sum"),
    ClaimCount_Sev=("ClaimAmount", "count")
)
df = freq.merge(sev_policy, on="IDpol", how="left")
```

**چرا دقیقاً همین‌طور؟** (استدلال معماری، عیناً از نوت‌بوک)
- **هم‌ترازی گرانولاریتی (۱:N → ۱:۱):** `freq` سطح بیمه‌نامه است ولی `sev` سطح خسارت (یک بیمه‌نامه می‌تواند چند خسارت داشته باشد)؛ گروه‌بندی روی `IDpol` قبل از merge از تکرار ردیف و اعوجاج در `Exposure` جلوگیری می‌کند.
- **`as_index=False` و Named Aggregation:** `IDpol` را به‌عنوان ستون معمولی نگه می‌دارد (نه Index) تا Join ساده بماند، و اسم‌گذاری صریح ستون‌ها از ایجاد `MultiIndex` شلوغ جلوگیری می‌کند.
- **`how="left"`:** تضمین می‌کند هر ۶۷۸,۰۱۳ بیمه‌نامه حفظ شود (یک `inner join` بیش از ۹۵٪ بیمه‌نامه‌های بدون خسارت را حذف می‌کرد و کل مدل‌سازی ریسک را نامعتبر می‌ساخت).

## ۶. نکته‌ی حیاتی کیفیت داده

بعد از Merge، بررسی `df["ClaimCostPerExposure"].isnull().value_counts()` نشان می‌دهد:

| مقدار | تعداد بیمه‌نامه | معنا |
|---|---|---|
| `True` (خالی/NaN) | ۶۵۳,۰۶۹ | بیمه‌نامه‌ی بدون خسارت (تطابقی در `sev_policy` نداشت) |
| `False` (پر) | ۲۴,۹۴۴ | بیمه‌نامه‌ی واقعاً خسارت‌دیده |

و به‌طور جداگانه: `freq["ClaimNb"].sum()` = **۳۶,۱۰۲** در برابر `len(sev)` = **۲۶,۴۴۴** — یعنی **۹,۱۱۷ بیمه‌نامه** دارای `ClaimNb ≥ 1` هستند ولی هیچ رکوردی در `sev` ندارند (هیچ‌وقت برعکس این حالت رخ نمی‌دهد).

**درس مهم (Survivor Bias Trap):** چون `TotalClaimCost` برای بیمه‌نامه‌های بدون خسارت `NaN` است (نه صفر)، `.mean()` در pandas به‌طور پیش‌فرض NaN را نادیده می‌گیرد (`skipna=True`)؛ اگر قبل از `fillna(0)` میانگین بگیرید، فقط روی ۲۴,۹۴۴ بیمه‌نامه‌ی خسارت‌دیده حساب می‌شود و ریسک پرتفوی به‌شدت بیش‌برآورد می‌شود. **همیشه `fillna(0)` را قبل از میانگین‌گیری سطح-پرتفوی اجرا کنید.**

## ۷. نقشه‌ی کامل سلول‌های نوت‌بوک

| بخش | سلول‌ها | محتوا |
|---|---|---|
| Import & Load | ۰-۲ | `pandas`, `matplotlib`, `seaborn`, `numpy` + خواندن دو CSV |
| بررسی ساختار داده | ۳-۱۱ | `info()`, `describe()`, بررسی رکورد تکراری، `freq.columns` |
| EDA بصری | ۱۲-۱۸ | ۵ هیستوگرام: DrivAge، VehAge، VehPower، BonusMalus، Density |
| KPI سطح کل پرتفوی | ۱۹-۳۰ | Claim Frequency، Average Severity، Total Claim Cost (نسخه‌ی اولیه) |
| تجمیع و Merge | ۳۱-۳۵ | `sev_policy` + `freq.merge(..., how="left")` → `df` |
| KPI سطح بیمه‌نامه | ۳۶-۵۰ | `HasClaim`، `ClaimCostPerExposure`، `fillna(0)`، `AvgClaimSeverity` |
| **Q1** | ۵۱-۵۵ | خلاصه‌ی سلامت کلی پرتفوی |
| **Q2** | ۵۶-۶۵ | بار خسارت به تفکیک `Region` |
| **Q3** | ۶۶-۷۲ | سگمنت سنی راننده (`pd.cut` → `DriverAgeGroup`) |
| **Q4** | ۷۳-۸۰ | پراکندگی Frequency در برابر Severity به تفکیک منطقه |
| **Q5** | ۸۱-۸۷ | تمرکز هزینه: Top X% Cost Share، منحنی لورنز، ضریب جینی |
| **Q6** | ۸۸-۹۵ | طبقه‌بندی ریسک سگمنت‌ها + نمودار چهارربعی برچسب‌دار |
| **Q7** | ۹۶-۱۰۴ | تعریف KPIهای پایش ماهانه + baseline فعلی |
| **Q8** | ۱۰۵-۱۱۲ | سه توصیه‌ی داده‌محور نهایی با شواهد عددی |

## ۸. متغیرهای کلیدی

| متغیر | تعریف | سلول |
|---|---|---|
| `df` | دیتافریم نهایی سطح بیمه‌نامه | ۳۳ |
| `df["HasClaim"]` | ۱ اگر `ClaimNb>0` وگرنه ۰ | ۳۷ |
| `df["ClaimCostPerExposure"]` | `TotalClaimCost / Exposure` | ۴۰، ۴۴ |
| `df["AvgClaimSeverity"]` | `TotalClaimCost / ClaimNb` با محافظ صفر | ۴۵ |
| `region_analysis` | KPI تجمیعی به تفکیک `Region` | ۵۸-۶۳ |
| `age_analysis` | KPI تجمیعی به تفکیک `DriverAgeGroup` | ۶۸ |
| `segment_risk_summary` | جدول نهایی طبقه‌بندی ریسک (Q6) | ۸۹ |

## ۹. خلاصه‌ی یافته‌های هر سؤال

| # | سؤال | یافته‌ی کلیدی |
|---|---|---|
| Q1 | سلامت کلی پرتفوی؟ | Frequency≈۱۰.۰۷٪ · Severity≈۱,۶۵۹ · هزینه‌ی کل≈۵۹.۹ میلیون |
| Q2 | بیشترین بار خسارت کجاست؟ | Centre و Rhone-Alpes (ناشی از حجم بالا، نه نرخ بالا) |
| Q3 | کدام سگمنت متفاوت است؟ | رانندگان ۱۸-۲۵: Frequency Index≈۱.۷۴، Severity Index≈۲.۶۱ |
| Q4 | پرفرکانس = پرهزینه؟ | خیر؛ Ile-de-France پرفرکانس/کم‌شدت، Champagne-Ardenne هر دو بالا |
| Q5 | هزینه متمرکز است؟ | Top 1% خسارت=۳۸٪ هزینه؛ Top 1% بیمه‌نامه=۷۴٪؛ Gini≈۰.۹۸۷ |
| Q6 | کدام سگمنت نیاز به بررسی دارد؟ | Champagne-Ardenne، Corse، رانندگان ۱۸-۲۵، برند B11 |
| Q7 | پایش ماهانه چیست؟ | ۱۲ KPI تعریف‌شده (داده فاقد فیلد تاریخ است) |
| Q8 | سه توصیه چیست؟ | رانندگان جوان، Bonus-Malus، بیمه اتکایی |

## ۱۰. سه توصیه‌ی نهایی

1. **بازنگری نرخ‌گذاری رانندگان ۱۸-۲۵ سال** — Frequency Index≈۱.۷۴x، Severity Index≈۲.۶۱x.
2. **تشدید اثر Bonus-Malus در قیمت‌گذاری** — فرکانس از ۸.۳٪ (BM ۵۰-۶۰) به ۵۶.۸٪ (BM ۱۵۱-۲۳۰) می‌رسد.
3. **تقویت بیمه‌ی اتکایی برای ریسک دنباله** — تنها ۱٪ از خسارات، ۳۸٪ هزینه‌ی کل را می‌سازند.

## ۱۱. خلاصه‌ی روش‌شناسی

- **Left Join** (نه Inner) تا بیمه‌نامه‌های بدون خسارت هم بمانند.
- **fillna(0)** روی ستون‌های تجمیعی خسارت.
- **تابع عمومی `analyze_segments`** برای تحلیل یکسان هر بعُد.
- **Risk Index** (نسبت به میانگین کل)، آستانه‌ی ۱.۵ برای پرچم «پرریسک».
- **Lorenz Curve و Gini Coefficient** برای تمرکز هزینه در توزیع‌های کج.
- **IQR Outlier Method** برای شناسایی خسارات غیرعادی.

## ۱۲. نحوه‌ی اجرا

```bash
pip install pandas numpy matplotlib seaborn scipy
jupyter notebook insurance_notebook_Q7_Q8.ipynb
```
مسیر فایل‌های CSV در سلول ۲ باید با مسیر واقعی شما جایگزین شود. سلول‌ها را به‌ترتیب از بالا به پایین اجرا کنید.

## ۱۴. محدودیت‌ها

- داده فاقد فیلد تاریخ/زمان است.
- ناسازگاری `ClaimNb`/`sev` باید در هر تحلیل آینده مدنظر باشد.
- برخی مقادیر `VehAge` تا ۱۰۰ سال مشکوک به خطای ثبت داده هستند و نیاز به اعتبارسنجی دارند.
- این تحلیل صرفاً اکتشافی است؛ برای قیمت‌گذاری واقعی به مدل GLM/ML با اعتبارسنجی آماری نیاز است.

## ۱۵. واژه‌نامه

| اصطلاح | معنا |
|---|---|
| Exposure | مدت پوشش بیمه‌ای به سال |
| Claim Frequency | تعداد خسارت به‌ازای هر واحد Exposure |
| Claim Severity | متوسط هزینه‌ی هر خسارت |
| Risk Index | نسبت مقدار یک سگمنت به میانگین کل پرتفوی |
| Lorenz Curve / Gini | ابزار اندازه‌گیری تمرکز/نابرابری هزینه‌ی خسارت |
| Offset (در مدل‌سازی) | متغیری که برای نرمال‌سازی نرخ (مثل Exposure) در مدل رگرسیون شمارشی استفاده می‌شود |

---
---

# 🇬🇧 English

## Table of Contents
1. Project Overview
2. Project Files
3. Full Data Dictionary (with actual `describe()` statistics)
4. EDA Findings Summary (directly from the notebook)
5. Merge & Aggregation Architecture
6. Critical Data Quality Note
7. Full Notebook Cell Map
8. Key Variables
9. Findings Summary per Question
10. Three Final Recommendations
11. Methodology Highlights
12. How to Run
13. Power BI Package
14. Limitations
15. Glossary

---

## 1. Project Overview

This project is a data-driven, educational analysis built on the well-known public **French MTPL2** dataset (Motor Third-Party Liability insurance). It assesses overall portfolio health, identifies high-risk segments, measures claim-cost concentration (tail risk), defines a monthly monitoring framework, and produces three business recommendations — through 8 guided analytical questions.

```
Claim Frequency        = Total Claims / Total Exposure
Average Claim Severity = Total Claim Cost / Number of Claims
Pure Premium             = Claim Frequency × Average Claim Severity
```

## 3. Full Data Dictionary (with actual `describe()` statistics)

### `freMTPL2freq.csv` — 678,013 rows × 12 columns

| Column | Type | Mean | Median | Min | Max | Description |
|---|---|---|---|---|---|---|
| `IDpol` | float64 | — | — | 1 | 6,114,330 | Unique policy identifier (join key) |
| `ClaimNb` | int64 | 0.053 | 0 | 0 | 16 | Number of claims reported for the policy |
| `Exposure` | float64 | 0.529 | 0.49 | 0.0027 | 2.01 | Insurance coverage duration, in years |
| `VehPower` | int64 | 6.45 | 6 | 4 | 15 | Vehicle engine power |
| `VehAge` | int64 | 7.04 | 6 | 0 | 100 | Vehicle age (years) |
| `DrivAge` | int64 | 45.5 | 44 | 18 | 100 | Driver age |
| `BonusMalus` | int64 | 59.76 | 50 | 50 | 230 | Bonus-Malus coefficient (50 = maximum discount) |
| `VehBrand` | string | — | — | — | — | Vehicle brand (coded, e.g. B1, B11...) |
| `VehGas` | string | — | — | — | — | Fuel type (Diesel/Regular) |
| `Area` | string | — | — | — | — | Residential area code |
| `Density` | int64 | 1,792 | 393 | 1 | 27,000 | Population density of the residential area |
| `Region` | string | — | — | — | — | Geographic region name (France) |

### `freMTPL2sev.csv` — 26,444 rows × 2 columns

| Column | Type | Mean | Median | Min | Max | Description |
|---|---|---|---|---|---|---|
| `IDpol` | int64 | — | — | 139 | 6,113,971 | Policy identifier associated with the claim |
| `ClaimAmount` | float64 | 2,265.5 | 1,172 | 1 | 4,075,401 | Paid claim amount |

> Note: the largest recorded claim amount (`4,075,401`) is nearly **3,500x** the median (`1,172`) — the same extreme skew that is quantified in Q5 using the Lorenz curve and Gini coefficient.

## 4. EDA Findings Summary (directly from the notebook)

- **`ClaimNb` is heavily zero-inflated:** over 75% of policies have zero claims (even the 75th percentile is 0); real-world modeling requires count-data models (Poisson, Negative Binomial, or Tweedie), not plain linear regression.
- **`Exposure` averages about half a year (0.53)** with a 75th percentile of 0.99 (most full policies are annual); Exposure must be used as an **offset** in any frequency model.
- **`DrivAge`** is fairly well-distributed between 18 and 100 (mean 45.5).
- **`BonusMalus`** is heavily skewed: over half of policies sit exactly at the base level of 50 (maximum discount).
- **`VehAge`** has a median of 6 years, but a maximum of 100 years — a possible sign of vintage vehicles or data-entry anomalies requiring validation.
- **`Density`** has extreme variance (median=393 vs. mean=1,792); the notebook recommends a log transform (`np.log1p`) before any modeling.

## 5. Merge & Aggregation Architecture (as documented in the notebook)

```python
sev_policy = sev.groupby("IDpol", as_index=False).agg(
    TotalClaimCost=("ClaimAmount", "sum"),
    ClaimCount_Sev=("ClaimAmount", "count")
)
df = freq.merge(sev_policy, on="IDpol", how="left")
```

**Why exactly this way?** (architectural rationale, taken from the notebook)
- **Granularity alignment (1:N → 1:1):** `freq` is at the policy level while `sev` is at the individual claim level (one policy can have multiple claims); grouping by `IDpol` first prevents row duplication and distortion of total `Exposure` during the merge.
- **`as_index=False` and named aggregation:** keeps `IDpol` as a regular column for a clean join, and named aggregation avoids a messy `MultiIndex`.
- **`how="left"`:** guarantees all 678,013 original policies are preserved (an inner join would discard over 95% of non-claimant policies, invalidating any risk model).

## 6. Critical Data Quality Note

After the merge, checking `df["ClaimCostPerExposure"].isnull().value_counts()` shows:

| Value | Policy Count | Meaning |
|---|---|---|
| `True` (NaN) | 653,069 | Non-claimant policy (no match in `sev_policy`) |
| `False` (populated) | 24,944 | Actual claimant policy with incurred loss cost |

Separately: `freq["ClaimNb"].sum()` = **36,102** vs. `len(sev)` = **26,444** — meaning **9,117 policies** have `ClaimNb ≥ 1` but no matching record at all in `sev` (the reverse never happens).

**Key lesson (the Survivor Bias Trap):** since `TotalClaimCost` is `NaN` (not zero) for non-claimants after the left join, pandas' `.mean()` silently skips `NaN` by default (`skipna=True`); computing `.mean()` before `fillna(0)` evaluates cost only across the 24,944 claimant policies, massively overestimating portfolio risk. **Always run `fillna(0)` before any portfolio-level averaging.**

## 7. Full Notebook Cell Map

| Section | Cells | Content |
|---|---|---|
| Import & Load | 0-2 | `pandas`, `matplotlib`, `seaborn`, `numpy` + reading the two CSVs |
| Data Structure Check | 3-11 | `info()`, `describe()`, duplicate check, `freq.columns` |
| Visual EDA | 12-18 | 5 histograms: DrivAge, VehAge, VehPower, BonusMalus, Density |
| Portfolio-Level KPIs | 19-30 | Claim Frequency, Average Severity, Total Claim Cost (initial version) |
| Aggregation & Merge | 31-35 | `sev_policy` + `freq.merge(..., how="left")` → `df` |
| Policy-Level KPIs | 36-50 | `HasClaim`, `ClaimCostPerExposure`, `fillna(0)`, `AvgClaimSeverity` |
| **Q1** | 51-55 | Overall portfolio health summary |
| **Q2** | 56-65 | Claim burden by `Region` |
| **Q3** | 66-72 | Driver age segmentation (`pd.cut` → `DriverAgeGroup`) |
| **Q4** | 73-80 | Frequency vs. Severity scatter plot by region |
| **Q5** | 81-87 | Cost concentration: Top X% Cost Share, Lorenz curve, Gini coefficient |
| **Q6** | 88-95 | Segment risk classification + labeled quadrant chart |
| **Q7** | 96-104 | Monthly monitoring KPI definitions + current baseline |
| **Q8** | 105-112 | Three final data-driven recommendations with supporting evidence |

## 8. Key Variables

| Variable | Definition | Cell(s) |
|---|---|---|
| `df` | Final policy-level dataframe | 33 |
| `df["HasClaim"]` | 1 if `ClaimNb>0`, else 0 | 37 |
| `df["ClaimCostPerExposure"]` | `TotalClaimCost / Exposure` | 40, 44 |
| `df["AvgClaimSeverity"]` | `TotalClaimCost / ClaimNb`, zero-guarded | 45 |
| `region_analysis` | Aggregated KPIs by `Region` | 58-63 |
| `age_analysis` | Aggregated KPIs by `DriverAgeGroup` | 68 |
| `segment_risk_summary` | Final risk classification table (Q6) | 89 |

## 9. Findings Summary per Question

| # | Question | Key Finding |
|---|---|---|
| Q1 | Overall portfolio health? | Frequency≈10.07% · Severity≈1,659 · Total cost≈59.9M |
| Q2 | Which regions carry the greatest burden? | Centre and Rhone-Alpes (driven by volume, not rate) |
| Q3 | Which segments differ most? | Drivers 18-25: Frequency Index≈1.74, Severity Index≈2.61 |
| Q4 | Does high frequency mean high severity? | No; Ile-de-France is high-frequency/low-severity, Champagne-Ardenne is high on both |
| Q5 | Is cost concentrated? | Top 1% of claims = 38% of cost; top 1% of policies = 74%; Gini≈0.987 |
| Q6 | Which segments need investigation? | Champagne-Ardenne, Corse, drivers 18-25, brand B11 |
| Q7 | What to monitor monthly? | 12 defined KPIs (no date field exists) |
| Q8 | Three recommendations? | Young drivers, Bonus-Malus, reinsurance |

## 10. Three Final Recommendations

1. **Re-price/re-underwrite drivers aged 18-25** — Frequency Index≈1.74x, Severity Index≈2.61x the portfolio average.
2. **Strengthen Bonus-Malus-based pricing** — frequency rises from 8.3% (BM 50-60) to 56.8% (BM 151-230).
3. **Reinforce reinsurance for tail risk** — only 1% of claims generate 38% of total claim cost.

## 11. Methodology Highlights

- **Left Join** (not Inner) so non-claiming policies remain in the analysis.
- **fillna(0)** on aggregated claim columns.
- **A reusable `analyze_segments` function** for consistent analysis across every dimension.
- **Risk Index** (relative to portfolio mean); a 1.5x threshold flags "high risk."
- **Lorenz Curve and Gini Coefficient** for cost concentration in a skewed distribution.
- **IQR Outlier Method** to flag unusual claims objectively.

## 12. How to Run

```bash
pip install pandas numpy matplotlib seaborn scipy
jupyter notebook insurance_notebook_Q7_Q8.ipynb
```
Replace the hard-coded CSV paths in cell 2 with your actual file locations. Run cells sequentially from top to bottom.

## 14. Limitations

- The data has no date/time field.
- The `ClaimNb`/`sev` mismatch should be kept in mind for future analysis.
- Some `VehAge` values up to 100 years look like potential data-entry errors and warrant validation.
- This is exploratory only; real-world pricing requires a GLM/ML model with statistical validation.

## 15. Glossary

| Term | Meaning |
|---|---|
| Exposure | Duration of insurance coverage, in years |
| Claim Frequency | Number of claims per unit of Exposure |
| Claim Severity | Average cost per claim |
| Risk Index | Ratio of a segment's value to the overall portfolio average |
| Lorenz Curve / Gini | Tools for measuring concentration/inequality in cost distribution |
| Offset (in modeling) | A variable (like Exposure) used to normalize rates in count-data regression models |
