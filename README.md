# Maternal Education and Neonatal Survival in Pakistan

This project looks at what predicts neonatal mortality in Pakistan, using survey-weighted descriptive statistics and logistic regression on the 2017-18 Pakistan Demographic and Health Survey (DHS).

## Overview

Neonatal mortality, defined as death within the first 28 days of life, is still a major public health problem in Pakistan despite progress on other child health indicators. This project looks at how maternal education, household wealth, urban/rural residence, place of delivery, maternal age, and birth order relate to neonatal death, using nationally representative DHS data and accounting for the survey's sampling design (clustering, stratification, and weighting).

Maternal education came out as the strongest and most consistent predictor of neonatal mortality. Mothers with secondary or higher education have substantially lower odds of neonatal death than mothers with no formal education, and that relationship holds even after adjusting for wealth, residence, delivery setting, maternal age, and birth order. Household wealth and urban/rural residence look like strong predictors in the raw descriptive statistics, but lose statistical significance once maternal education is in the model. That suggests education is picking up on broader disadvantages that wealth or residence alone don't fully capture.

## Data

This analysis uses the **Birth Recode file (PKBR71)** from the **2017-18 Pakistan Demographic and Health Survey**, a nationally representative household survey with a stratified two-stage sampling design (clusters selected by probability proportional to size, then households within clusters).

- **Unit of analysis:** birth (n = 50,495)
- **Outcome:** `neonatal_death`, a binary indicator built from survival status (`b5`) and age at death in days (`b7`). Coded 1 if a child died within 28 days, 0 if survived, and set to missing when there wasn't enough information to classify the outcome.

| Variable | Description |
|---|---|
| `b5`, `b7` | Child survival status; age at death (days) |
| `v106` | Maternal education level |
| `v190` | Household wealth quintile |
| `v025` | Urban / rural residence |
| `m15` | Place of delivery (recoded into facility vs. home) |
| `v012` | Maternal age |
| `bord` | Birth order |
| `b2` | Birth year |
| `v005`, `v021`, `v022` | Survey weight, primary sampling unit, strata (for `svydesign`) |

DHS microdata isn't redistributed in this repo. To reproduce the analysis, request the Pakistan 2017-18 Birth Recode (PKBR71) dataset through the [DHS Program data request portal](https://dhsprogram.com/data/), then place `PKBR71FL.DTA` in a local `/data` folder before running the notebook.

## Methods

- Survey design specified with `survey::svydesign()` (PSU = `v021`, strata = `v022`, weights = `v005 / 1,000,000`), consistent with DHS analysis guidelines
- Descriptive comparisons (neonatal mortality by education, wealth, residence) estimated with `svyby()` and reported with 95% confidence intervals
- Multivariable model: survey-weighted logistic regression (`svyglm()`, quasibinomial family) predicting neonatal death from maternal education, wealth, residence, delivery type, maternal age, birth order, and birth year (mean-centered)
- Results reported as odds ratios with 95% confidence intervals

## Repository Structure

```
.
├── README.md
├── Pakistan_Neonatal.ipynb    # R notebook: data cleaning, EDA, descriptive stats, regression model
└── data/                      # not included, see Data section above
```

## Reproducing the Analysis

Requirements: R (4.x) with the following packages:

```r
install.packages(c("tidyverse", "survey", "janitor", "patchwork", "haven"))
```

1. Obtain `PKBR71FL.DTA` via the DHS Program (see Data section above) and place it in `/data`
2. Open `Pakistan_Neonatal.ipynb` in Jupyter or RStudio
3. Run all cells in order. The notebook builds the analytic dataset, generates the descriptive figures, and fits the regression model.

## Key Findings

- Maternal education is the strongest predictor of neonatal mortality after adjustment; higher education is associated with significantly lower odds of neonatal death
- Household wealth and urban/rural residence show strong unadjusted associations with neonatal mortality, but are not statistically significant once maternal education and other covariates are included
- Place of delivery (facility vs. home) is not a statistically significant predictor after adjustment
- Older maternal age is associated with lower neonatal mortality risk; birth order shows a weak positive association
- Findings are consistent with prior demographic research identifying female education as a key driver of child survival outcomes

## Limitations

This is an observational, cross-sectional analysis, so associations can't be interpreted causally. DHS data is also subject to recall and reporting biases (particularly for variables like place of delivery), and some DHS-specific missing-value codes required careful recoding to avoid misclassifying neonatal deaths.

## Author

Jeren Gochyyeva

## References

Sources referenced in this analysis:

- Ariff, S., Soofi, S. B., Sadiq, K., Feroze, A. B., Khan, S., Jafarey, S. N., Ali, N., & Bhutta, Z. A. (2010). Evaluation of health workforce competence in maternal and neonatal issues in public health sector of Pakistan: an assessment of their training needs. *BMC Health Services Research*, 10(319).
- Bhutta, Z. A., et al. (2013). Reproductive, maternal, newborn, and child health in Pakistan: challenges and opportunities. *PubMed*.
- Caldwell, J. C. (1986). Routes to Low Mortality in Poor Countries. *Population and Development Review*, 12(2).
- Dhaded, S. M., et al. (2022). The causes of preterm neonatal deaths in India and Pakistan (PURPOSe): a prospective cohort study. *The Lancet Global Health*, 10(11).
- Lutz, W., & Kebede, E. (2018). Education and Health: Redrawing the Preston Curve. *Population and Development Review*, 44(2).
- Muzzamil, M., et al. (2022). The survival rate of neonates in Pakistan: Problems in health care access, quality and recommendations. *PMC*.
- Our World in Data (2020). Estimated maternal mortality ratio, processed from UN MMEIG data.
- UNICEF (2026). Levels and trends in child mortality.
- World Bank Gender Data Portal (2026). Literacy rate (%).
- World Health Organization (2024). Newborn mortality.
