# 💳 Credit Choice Experiment — Discrete Choice Modeling

A discrete-choice analysis of how the **visual salience of credit terms** in digital advertising affects consumer choices over loan offers. The data come from a controlled experiment in which participants were randomly assigned to four ad-design conditions and asked to choose between credit alternatives across multiple scenarios.

The analysis combines **descriptive EDA**, **conditional logit (mlogit)**, **mixed logit with unobserved heterogeneity**, and a **machine learning model comparison** (CART, SVM, KNN, Random Forest) to ask both *why* people choose what they choose and *how well we can predict it*.

![R](https://img.shields.io/badge/R-4.3-276DC3?logo=r&logoColor=white)
![mlogit](https://img.shields.io/badge/mlogit-discrete%20choice-blue)
![caret](https://img.shields.io/badge/caret-ML-orange)
![Status](https://img.shields.io/badge/Status-Completed-green)

---

## 🎯 Research Question

> **Does the way credit information is visually displayed in digital ads change which offers consumers choose? And does it change the *consistency* of those choices relative to a baseline where the same offers were shown without advertising material?**

The motivating policy question: should regulators force credit advertisers to display key financial information (APR, total cost, installment value) in a more prominent, standardized way?

---

## 🧪 Experimental Design

Participants were randomly assigned to one of **four conditions**:

| Group | Description |
|---|---|
| **GC (Control)** | Typical layout used today in the Chilean credit market |
| **T1** | Total cost of credit (CTC) is visually emphasized |
| **T2** | Both total cost (CTC) and installment value (VC) are emphasized |
| **T3** | All financial information is emphasized vs. other visual elements |

Each participant made **6 binary choices** across three macro-scenarios (TV purchase / vacation / unexpected medical bill). Their choices in this wave (wave 2) are compared to an earlier wave (wave 1) where the same offers were presented as plain financial tables — providing a clean within-subject **consistency** measure.

---

## 📊 Dataset

`credit-choice.csv` — long format, two rows per binary choice.

Key columns:

| Column | Description |
|---|---|
| `choice_id`, `idp` | Choice id and participant id |
| `feat.CAE`, `feat.CTC`, `feat.VC`, `feat.CantCuotas` | Offer attributes (APR, total cost, installment value, # installments) |
| `choice` | Binary — was this alternative picked? |
| `condicion_experimental` | GC / T1 / T2 / T3 |
| `macroescenario` | TV / Travel / Health |
| `monto_oferta` | Loan amount (200K / 1M / 400K) |
| `dem.mujer`, `dem.lit_fin`, `dem.liquidez` | Gender, financial literacy score, monthly liquidity |
| `ola2.grado_consistencia` | # of times the participant was consistent with their wave-1 choices |
| `ola2.error_estimacion_*` | Errors in estimating offer cost / installment after exposure |

---

## 🔬 Methodology

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

## 📈 Key Findings

1. **Offer attributes matter most.** Across all models the strongest predictors of choice are the **APR (CAE)** and the **number of installments** — visual treatment is a second-order effect compared to the underlying financials.
2. **Conditional logits (modelp1, modelp2) found no significant T3 effect** on choice. Standard discrete-choice analysis suggests that emphasizing financial information does *not* meaningfully change which offer people pick.
3. **The mixed logit (modelp3) tells a different story.** Once we allow for unobserved heterogeneity across participants, T3 *does* show a significant effect on the probability of choosing the left alternative, suggesting that simpler logits were masking real but heterogeneous responses.
4. **No interaction with demographics is significant.** Being a woman, having higher financial literacy, or correctly estimating costs does not significantly modulate the T3 effect — at least not at this sample size.
5. **For the policy question**: the evidence is **inconclusive**. The simpler models do not support mandating a T3-style format, but the mixed-logit and EDA hints suggest the effect may exist for users who understand offers poorly. From an ethical standpoint a T3-style minimum standard is still defensible — it guarantees all users see the relevant information at the same visual weight.
6. **For prediction**: Random Forest gave the best test-set accuracy among the ML models, but logit/mixed-logit remain preferable when the goal is to *interpret* the role of each variable.

---

## 🛠️ Tech Stack

`R` `mlogit` `caret` `rpart` `kernlab` `randomForest` `ggplot2` `dplyr` `kableExtra`

---

## 🗂️ Project Structure

```
credit-choice-experiment/
├── README.md
├── analysis.Rmd          # Full analysis (EDA + mlogit + ML)
└── data/
    └── README.md         # Schema reference
```

---

## 🚀 How to Reproduce

```r
install.packages(c("mlogit", "caret", "rpart", "kernlab",
                   "randomForest", "ggplot2", "dplyr", "kableExtra"))

rmarkdown::render("analysis.Rmd")
```

---

## 📚 Context

Originally developed for **IN5162 — Marketing Engineering**, taught by Prof. Marcel Goic at the **Universidad de Chile (Industrial Engineering, 2023)**. Reframed and rewritten in English for portfolio presentation.

---

## 👤 Author

**Jonathan Sánchez**
- GitHub: [@jsanchez-ds](https://github.com/jsanchez-ds)
- Universidad de Chile — Industrial Engineering
