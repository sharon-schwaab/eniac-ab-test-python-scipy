<div align="center">

# 🧪 Eniac A/B Test Analysis

### Statistical Experiment Analysis with Python & SciPy

**Which call-to-action should Eniac use on its homepage?**

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![pandas](https://img.shields.io/badge/pandas-Data%20Analysis-150458?logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![SciPy](https://img.shields.io/badge/SciPy-Statistics-8CAAE6?logo=scipy&logoColor=white)](https://scipy.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)](https://jupyter.org/)

**A/B/C/D testing · Hypothesis testing · Chi-square · Confidence intervals · Power analysis**

</div>

---

## 📌 Project Overview

Eniac is an online retailer for **Apple products and accessories**.

The company tested four versions of its homepage call-to-action button to determine which design generated the highest **click-through rate (CTR)**.

The analysis goes beyond simply comparing percentages.

It investigates:

- whether the observed differences are statistically significant,
- which variants differ from each other,
- whether the experiment had enough statistical power,
- and whether Eniac should actually replace its current button.

![CTR per version with 95% confidence interval](images/ctr_confidence_intervals.png)

---

## 🎯 Business Question

> **Which button version should Eniac use on its homepage?**

Four combinations of **button color** and **button text** were tested:

| Version | Color | CTA text |
|:---:|:---:|:---:|
| **A — Current** | White | `SHOP NOW` |
| **B** | Red | `SHOP NOW` |
| **C** | White | `SEE DEALS` |
| **D** | Red | `SEE DEALS` |

### Primary Metric

**Click-through rate**

\[
CTR = \frac{\text{Button Clicks}}{\text{Homepage Visits}}
\]

A variant is only considered meaningfully better if its difference is **statistically significant**.

---

## ⚙️ Experiment Setup

| Parameter | Value |
|---|---|
| Test type | A/B/C/D experiment |
| Test duration | 14 days |
| Period | 2021-11-02 → 2021-11-16 |
| Traffic | ~25,000 visits per version |
| Significance level | α = 0.05 |
| Statistical power | 80% |
| Minimum detectable effect | 20% |

---

## 📊 Key Results

| Version | Visits | Clicks | CTR |
|:---:|---:|---:|---:|
| **A** | 25,326 | 512 | **2.02%** |
| **B** | 24,747 | 281 | **1.14%** |
| **C** | 24,876 | 527 | **2.12%** |
| **D** | 25,233 | 193 | **0.76%** |

### Main Findings

✅ **The variants do not perform equally**

A chi-square test across all four versions shows a highly significant relationship between button version and click behavior:

**χ² = 224.02 · df = 3 · p < 0.001**

---

🔴 **Red buttons perform substantially worse**

Both white variants outperform both red variants in the pairwise comparisons.

The button color therefore appears to be one of the strongest signals in the experiment.

---

⚖️ **A and C are statistically indistinguishable**

Version C records the highest raw CTR:

**2.12% vs. 2.02% for A**

However, the difference is not statistically significant:

**p = 0.465**

The observed relative improvement is only approximately **4.8%**.

---

⏱️ **Detecting such a small difference would require a much longer experiment**

The original experiment was designed around a **20% minimum detectable effect**.

Detecting the observed difference between A and C reliably would require approximately:

> **95 days of A vs. C testing**

instead of the original 14-day experiment.

---

📉 **Secondary metrics favor Version A**

Additional behavioral metrics provide a useful tie-breaker:

| Metric | Version A | Version C |
|---|---:|---:|
| Drop-off rate | **~62%** | ~71% |
| Homepage-return rate | Slightly lower | Slightly higher |

Version C therefore offers no statistically proven CTR advantage while showing a considerably higher drop-off rate.

---

## 🏆 Business Recommendation

> ### Keep Version A — White / SHOP NOW

Version C achieved the highest observed CTR, but its advantage over the existing Version A is **not statistically significant**.

Switching from A to C would therefore provide **no demonstrated improvement** based on the available experiment.

At the same time, Version A performs better on the available drop-off metric.

### Recommended actions

- ✅ Keep **Version A** as the homepage CTA
- ❌ Do not introduce the red variants B or D
- 🔬 Run a dedicated **A vs. C experiment** only if detecting small CTR differences is strategically valuable

---

## 🔬 Analysis Approach

The analysis follows a structured experimentation workflow.

### 1. Data Extraction

The source files are click-tracking exports containing one row per homepage element.

- Homepage visits were extracted from a text field using a **regular expression**
- Button clicks were extracted from the CTA button row
- The four datasets were transformed into a common analytical structure

---

### 2. Exploratory Analysis

CTR was calculated for every version:

\[
CTR = \frac{Clicks}{Visits}
\]

To visualize the uncertainty around each estimate, **95% Wilson confidence intervals** were calculated.

---

### 3. Global Chi-Square Test

A contingency table containing:

- clicks
- non-clicks

was constructed for all four versions.

A **chi-square test of independence** was then used to test whether click behavior depends on the button version.

---

### 4. Pairwise Post-Hoc Tests

Because the global test only tells us that **at least one version differs**, all six possible variant pairs were tested individually.

To control the increased false-positive risk from multiple comparisons, a **Bonferroni correction** was applied.

\[
\alpha_{adjusted} = \frac{0.05}{6} \approx 0.0083
\]

<p align="center">
  <img src="images/pairwise_pvalues_heatmap.png" width="48%" alt="Pairwise p-values heatmap">
  <img src="images/power_curve.png" width="48%" alt="Required test duration by effect size">
</p>

---

### 5. Power Analysis

A statistical power analysis was used to estimate the sample size required to reliably detect smaller differences between A and C.

This demonstrates an important experimentation principle:

> **Small effects require disproportionately larger samples.**

Halving the effect size roughly quadruples the required sample size.

---

### 6. Secondary Metrics

CTR alone does not describe the full user journey.

Two additional metrics were therefore considered:

- **Drop-off rate**
- **Homepage-return rate**

![Additional metrics](images/additional_metrics.png)

These metrics were used as supporting evidence when comparing the two strongest variants, A and C.

---

## 🧠 Statistical Concepts

### χ² — Chi-Square Statistic

The chi-square statistic measures how far the **observed frequencies** differ from the frequencies we would expect if all button versions performed equally.

A larger χ² value indicates a larger discrepancy.

The statistic itself is interpreted together with the **degrees of freedom** and **p-value**.

---

### Degrees of Freedom

For a contingency table:

\[
df = (rows - 1)(columns - 1)
\]

For four versions and two click outcomes:

\[
(4 - 1)(2 - 1) = 3
\]

Therefore:

**df = 3**

---

### p-value

The p-value describes how likely it would be to observe differences at least this large if the null hypothesis were true.

Here, the null hypothesis states that:

> **All four button versions have the same CTR.**

If:

\[
p < \alpha
\]

the null hypothesis is rejected.

For this experiment:

**p < 0.001**

so the global differences are statistically significant.

---

### Scientific Notation

Statistical software often displays extremely small values using scientific notation.

For example:

```text
2.72e-48
```

means:

```text
2.72 × 10⁻⁴⁸
```

For practical reporting, a value this small is normally written as:

**p < 0.001**

---

### Bonferroni Correction

Running several hypothesis tests increases the probability of finding at least one significant result purely by chance.

With six independent tests at α = 0.05:

\[
1 - 0.95^6 \approx 26\%
\]

The Bonferroni correction reduces this risk by dividing the significance threshold by the number of tests:

\[
\frac{0.05}{6} \approx 0.0083
\]

The original p-values do **not** change.

Only the threshold for calling a result statistically significant becomes stricter.

---

### “Not Significant” ≠ “Equal”

A non-significant result does not prove that two variants perform exactly the same.

It means that the experiment could not reliably distinguish the observed difference from random variation.

The A/B/C/D experiment was designed to detect effects of approximately **20% or larger**.

The observed difference between A and C was only around **4.8%**, making it much harder to detect.

---

### Statistical Power

Power describes the probability that an experiment detects an effect when that effect genuinely exists.

This experiment was designed for:

**80% power**

Power depends primarily on:

- sample size,
- effect size,
- significance level,
- and baseline conversion rate.

Smaller effects require much larger datasets.

---

## ⚠️ Limitations

Several limitations should be considered when interpreting the analysis.

**Secondary metrics**

Drop-off and homepage-return rates were only available as percentages from a chart in the course material.

Because the underlying counts were unavailable, statistical significance tests could not be performed for these metrics.

Data for Version B was also missing because of a collection error.

**Visits vs. users**

CTR is based on visits rather than unique users.

A single person may therefore contribute more than one observation.

**Variant creation dates**

Version A was created on `2021-09-14`, while versions B–D were created on `2021-10-27`.

All variants nevertheless report the same 14-day experiment duration.

The creation dates therefore most likely describe page setup rather than the actual experimental period.

---

## 📁 Project Structure

```text
eniac-ab-test/
│
├── data/
│   ├── eniac_a.csv
│   ├── eniac_b.csv
│   ├── eniac_c.csv
│   └── eniac_d.csv
│
├── images/
│   ├── ctr_confidence_intervals.png
│   ├── pairwise_pvalues_heatmap.png
│   ├── power_curve.png
│   └── additional_metrics.png
│
├── notebooks/
│   └── ab_test_analysis.ipynb
│
├── README.md
└── requirements.txt
```

---

## 🚀 Running the Project

Clone the repository:

```bash
git clone https://github.com/sharon-schwaab/eniac-ab-test.git
cd eniac-ab-test
```

Install the required packages:

```bash
python -m pip install -r requirements.txt
```

Then open:

```text
notebooks/ab_test_analysis.ipynb
```

in **Jupyter Notebook**, **JupyterLab**, or **VS Code** and run all cells.

---

## 🛠️ Tech Stack

| Area | Tools |
|---|---|
| Language | Python |
| Data manipulation | pandas · NumPy |
| Statistical testing | SciPy |
| Statistical modelling | statsmodels |
| Visualization | Matplotlib · seaborn |
| Development | Jupyter Notebook |

---

## 💡 Skills Demonstrated

This project demonstrates practical experience with:

- A/B testing and experimental design
- hypothesis testing
- chi-square tests
- post-hoc analysis
- multiple-testing correction
- confidence intervals
- statistical power analysis
- sample-size estimation
- regular-expression data extraction
- Python data analysis
- statistical visualization
- translating statistical results into business decisions

---

## 🎓 Project Context

This project was completed as part of a **Data Analytics bootcamp**.

It is the third Eniac case study and builds on previous projects involving:

1. **SQL & Tableau** — partnership and market analysis
2. **Python** — discount strategy analysis
3. **Python & SciPy** — A/B test and statistical experiment analysis

The focus of this project is not only calculating statistical results, but translating them into a **defensible business recommendation**.

---

<div align="center">

## 📬 Contact

<p align="center">

<a href="mailto:sharon.schwaab@outlook.de">
  <img src="https://img.shields.io/badge/Email-sharon.schwaab%40outlook.de-0078D4?style=for-the-badge&logo=microsoftoutlook&logoColor=white" alt="Email">
</a>

<a href="https://www.linkedin.com/in/sharon-schwaab/">
  <img src="https://img.shields.io/badge/LinkedIn-Sharon%20Schwaab-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn">
</a>

<a href="https://github.com/sharon-schwaab">
  <img src="https://img.shields.io/badge/GitHub-sharon--schwaab-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub">
</a>

</p>

### 🧪 From experiment data to business decision.

**Python · Statistics · A/B Testing · Data Analytics**

</div>
