# Credit Choice Experiment — Discrete Choice Modeling

A discrete-choice analysis of how the **visual salience of credit terms** in digital advertising affects consumer choices over loan offers. Participants in a controlled experiment were randomly assigned to four ad-design conditions and asked to choose between credit alternatives across multiple scenarios.

The analysis combines **descriptive EDA**, **conditional logit (mlogit)**, **mixed logit with unobserved heterogeneity**, and a **machine-learning model comparison** (CART, SVM, KNN, Random Forest) to ask both *why* people choose what they choose and *how well we can predict it*.

[![Render Rmd](https://github.com/jsanchez-ds/credit-choice-experiment/actions/workflows/render.yml/badge.svg)](https://github.com/jsanchez-ds/credit-choice-experiment/actions/workflows/render.yml)
[![View Report](https://img.shields.io/badge/View_Report-GitHub_Pages-2ea44f?logo=github)](https://jsanchez-ds.github.io/credit-choice-experiment/)
![R](https://img.shields.io/badge/R-4.3-276DC3?logo=r&logoColor=white)
![mlogit](https://img.shields.io/badge/mlogit-discrete%20choice-blue)
![caret](https://img.shields.io/badge/caret-ML-orange)

> **[→ Read the full rendered report](https://jsanchez-ds.github.io/credit-choice-experiment/)** — every plot, table, and model output, no R installation required.

---

## Research Question

> **Does the way credit information is visually displayed in digital ads change which offers consumers choose? And does it change the *consistency* of those choices relative to a baseline where the same offers were shown without advertising material?**

The motivating policy question: should regulators force credit advertisers to display key financial information (APR, total cost, installment value) in a more prominent, standardized way?

---

## Experimental Design

Participants were randomly assigned to one of **four conditions**:

| Group | Description |
|---|---|
| **GC (Control)** | Typical layout used today in the Chilean credit market |
| **T1** | Total cost of credit (CTC) is visually emphasized |
| **T2** | Both total cost (CTC) and installment value (VC) are emphasized |
| **T3** | All financial information is emphasized vs. other visual elements |

Each participant made **6 binary choices** across three macro-scenarios (TV purchase / vacation / unexpected medical bill). Their choices in this wave (wave 2) are compared to an earlier wave (wave 1) where the same offers were presented as plain financial tables — providing a clean within-subject **consistency** measure.

---

## Dataset

The analysis Rmd reads from `data/sample.csv` — a **fully synthetic** sample of ~265 rows (≈132 binary choices, 22 simulated participants) generated to:

- Respect the original schema column-by-column
- Match the marginal distributions of `condicion_experimental` and `macroescenario`
- Reproduce the basic mechanic that participants tend to pick the alternative with lower total cost (CTC)

The original 8,000-row dataset is **not redistributed** because it was provided under the terms of an academic course. See [`data/README.md`](data/README.md) for the full schema.

---

## Methodology

### 1. EDA
- Demographics (gender, financial literacy, monthly liquidity)
- Choice rates across the four offer attributes
- Consistency vs. wave 1 by experimental group
- Comprehension errors (CTC / VC / APR recall) by experimental group

### 2. Discrete Choice Models (`mlogit`)
| Model | Type | Notes |
|---|---|---|
| `modelp1` | Conditional logit | Baseline: only `CAE + CantCuotas` as alternative-specific attributes |
| `modelp2` | Conditional logit + interactions | Adds participant-level interactions: `mujer × T3`, `lit_fin × T3`, `estima_bien × T3`, scenario fixed effects |
| `modelp3` | **Mixed logit** | Same spec + random coefficients with correlation, capturing **unobserved heterogeneity** across participants |

### 3. ML Comparison (caret)
Predictive comparison on a held-out test split:
- **CART** (`rpart`)
- **SVM linear** (`svmLinear`)
- **KNN** (`knn`)
- **Random Forest** (`rf`)

Models compared via `resamples()` on accuracy and Kappa.

---

## Key Findings

1. **Offer attributes matter most.** Across all models the strongest predictors of choice are the **APR (CAE)** and the **number of installments** — visual treatment is a second-order effect compared to the underlying financials.
2. **Conditional logits (modelp1, modelp2) found no significant T3 effect** on choice. Standard discrete-choice analysis suggests that emphasizing financial information does *not* meaningfully change which offer people pick.
3. **The mixed logit (modelp3) tells a different story.** Once we allow for unobserved heterogeneity across participants, T3 *does* show a significant effect on the probability of choosing the left alternative, suggesting that simpler logits were masking real but heterogeneous responses.
4. **No interaction with demographics is significant.** Being a woman, having higher financial literacy, or correctly estimating costs does not significantly modulate the T3 effect — at least not at this sample size.
5. **Policy implication**: the evidence is **inconclusive** but suggests a T3-style minimum standard is defensible — it guarantees all users see the relevant information at the same visual weight, even if the average behavioral effect is small.
6. **Prediction**: Random Forest gave the best test-set accuracy among the ML models, but logit / mixed-logit remain preferable when the goal is to *interpret* the role of each variable.

---

## Tech Stack

`R` `mlogit` `caret` `rpart` `kernlab` `randomForest` `ggplot2` `dplyr` `kableExtra`

---

## Project Structure

```
credit-choice-experiment/
├── README.md
├── analysis.Rmd                    # Full analysis (EDA + mlogit + ML)
├── data/
│   ├── README.md                   # Schema reference
│   └── sample.csv                  # Synthetic 265-row sample
└── .github/workflows/
    └── render.yml                  # CI: render Rmd → HTML → Pages
```

---

## How to Reproduce

### Option A — Read the rendered report (no install)

Just open <https://jsanchez-ds.github.io/credit-choice-experiment/>. The CI rebuilds the report on every push to `main`.

### Option B — Run locally

```r
install.packages(c(
  "rmarkdown", "knitr", "tidyverse", "dplyr", "readr", "ggplot2",
  "gridExtra", "ggcorrplot", "psych", "mlogit", "fixest", "glmnet",
  "caret", "kernlab", "earth", "randomForest", "kableExtra",
  "stargazer", "modelsummary", "gt"
))

rmarkdown::render("analysis.Rmd", output_dir = "docs", output_file = "index.html")
```

The Rmd reads `data/sample.csv` from the repo, so no data download is needed.

---

## Context

Originally developed for **IN5162 — Marketing Engineering**, taught by Prof. Marcel Goic at the **Universidad de Chile (Industrial Engineering, 2023)**. Reframed and rewritten in English for portfolio presentation.

---

## Author

**Jonathan Sánchez**
- GitHub: [@jsanchez-ds](https://github.com/jsanchez-ds)
- Universidad de Chile — Industrial Engineering
