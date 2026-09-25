# Eniac A/B Test Analysis with Python & SciPy

Statistical evaluation of an A/B/C/D test on the homepage of **Eniac**, an online retailer for Apple products and accessories.
Four versions of the main call-to-action button were tested to find out which one gets the most clicks, and whether the differences are real or just chance.

![CTR per version with 95% confidence interval](images/ctr_confidence_intervals.png)

## Business Question

Which button version should Eniac use on its homepage?

| Version | Color | Text |
|---|---|---|
| A (current) | white | SHOP NOW |
| B | red | SHOP NOW |
| C | white | SEE DEALS |
| D | red | SEE DEALS |

**Primary metric:** click-through rate (CTR) = clicks on the button / visits to the homepage.
A version only counts as better if its CTR difference is **statistically significant**.

**Test setup:** 14 days (2021-11-02 to 2021-11-16), roughly 25,000 visits per version, significance level α = 0.05, power = 80%, minimum detectable effect = 20%.

## Key Results

| Version | Visits | Clicks | CTR |
|---|---|---|---|
| A | 25,326 | 512 | 2.02% |
| B | 24,747 | 281 | 1.14% |
| C | 24,876 | 527 | 2.12% |
| D | 25,233 | 193 | 0.76% |

- **The versions differ:** chi-square test across all four versions, χ² = 224.02, df = 3, p < 0.001.
- **White beats red:** Both white versions perform significantly better than both red versions.
- **No single winner:** Version C has the highest CTR, but its lead over A is **not significant** (p = 0.465).
- **Too small to detect:** C is only 4.8% better than A in relative terms. Detecting that reliably would take about 95 days of testing A vs C alone.
- **Tie-breaker:** On the additional metrics, A has a much lower drop-off rate than C (~62% vs ~71%), while C is only slightly better on homepage-return rate.

**Recommendation: keep Version A (white, SHOP NOW).** Switching to C offers no proven benefit, and red buttons should not be introduced.

## Approach

1. **Data extraction:** The raw data are click-tracking exports with one row per page element. Visits were extracted from a text field with a regular expression, clicks from the button row.
2. **Descriptive analysis:** CTR per version with 95% Wilson confidence intervals.
3. **Chi-square test** on a contingency table (clicks vs. no clicks per version) to test whether CTR depends on the version.
4. **Post-hoc tests:** pairwise chi-square tests for all six pairs, with Bonferroni correction.
5. **Power analysis** to estimate how long a test would need to run to detect the small difference between A and C.
6. **Additional metrics** (drop-off rate and homepage-return rate) to decide between the two best versions.

<p align="center">
  <img src="images/pairwise_pvalues_heatmap.png" width="48%" alt="Pairwise p-values heatmap">
  <img src="images/power_curve.png" width="48%" alt="Required test duration by effect size">
</p>

![Additional metrics](images/additional_metrics.png)

## Statistical Concepts Explained

**Chi-square statistic (χ²):** Measures how far the observed click counts are from the counts we would expect if all versions performed equally. The larger the value, the bigger the deviation. On its own it is hard to interpret, which is why we look at the p-value.

**Degrees of freedom (df):** For a contingency table, (rows − 1) × (columns − 1). Here (4 − 1) × (2 − 1) = 3. It determines which reference distribution the test uses.

**p-value:** The probability of seeing differences at least this large if, in reality, all versions had the same CTR. If it is below the significance level α (here 0.05), we reject the null hypothesis.

**Reading scientific notation:** `2.72e-48` means 2.72 × 10⁻⁴⁸. The negative exponent moves the decimal point 48 places to the left: 0.000…000272, with 47 zeros after the decimal point. In practice, such values are reported as p < 0.001.

**Bonferroni correction:** Every test at α = 0.05 carries a 5% risk of a false positive. With six pairwise tests, the risk of at least one false positive rises to about 1 − 0.95⁶ ≈ 26%. The Bonferroni correction divides α by the number of tests (0.05 / 6 ≈ 0.0083), so a pairwise difference only counts as significant if its p-value is below 0.0083. The p-values themselves stay the same; only the threshold becomes stricter.

**"Not significant" is not "proven equal":** A non-significant result means the test could not distinguish the difference from chance. It does not prove that there is no difference. This test was designed to detect differences of at least 20%, so a 4.8% difference was always likely to go undetected.

**Power analysis:** Calculates the sample size needed to detect an effect of a given size with a given probability (power, here 80%). Halving the effect size roughly quadruples the required sample size, which is why small differences need very long tests.

## Limitations

- Drop-off and homepage-return rates are only available as percentages (read from a chart in the course material), so no significance test was possible for them. Data for Version B is missing due to a collection error.
- CTR counts clicks per visit, not unique users.
- According to the snapshot info, Version A was created earlier (2021-09-14) than versions B–D (2021-10-27). All four report the same 14-day duration, so this likely reflects page setup rather than the test period.

## Project Structure

```
eniac-ab-test/
├── data/
│   ├── eniac_a.csv
│   ├── eniac_b.csv
│   ├── eniac_c.csv
│   └── eniac_d.csv
├── images/
├── notebooks/
│   └── ab_test_analysis.ipynb
├── README.md
└── requirements.txt
```

## How to Run

```bash
git clone https://github.com/sharon-schwaab/eniac-ab-test.git
cd eniac-ab-test
python -m pip install -r requirements.txt
```

Then open `notebooks/ab_test_analysis.ipynb` in Jupyter or VS Code and run all cells.

## Tech Stack

Python · pandas · NumPy · SciPy · statsmodels · Matplotlib · seaborn · Jupyter

## Context

This project was completed as part of a Data Analytics bootcamp. It is the third case study for Eniac, following a [SQL & Tableau partnership analysis](https://github.com/sharon-schwaab) and a Python discount strategy analysis.
