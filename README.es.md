🌐 [English](README.md) · **Español**

# Experimento de Elección de Crédito — Modelamiento de Elección Discreta

Análisis de elección discreta sobre cómo la **saliencia visual de los términos de crédito** en publicidad digital afecta las elecciones del consumidor entre ofertas de préstamo. Los participantes en un experimento controlado fueron asignados aleatoriamente a cuatro condiciones de diseño de aviso y se les pidió elegir entre alternativas de crédito a través de múltiples escenarios.

El análisis combina **EDA descriptivo**, **logit condicional (mlogit)**, **logit mixto con heterogeneidad no observada** y una **comparación con modelos de machine learning** (CART, SVM, KNN, Random Forest) para preguntar tanto el *por qué* elige la gente lo que elige, como *qué tan bien podemos predecirlo*.

[![Render Rmd](https://github.com/jsanchez-ds/credit-choice-experiment/actions/workflows/render.yml/badge.svg)](https://github.com/jsanchez-ds/credit-choice-experiment/actions/workflows/render.yml)
[![View Report](https://img.shields.io/badge/Ver_Reporte-GitHub_Pages-2ea44f?logo=github)](https://jsanchez-ds.github.io/credit-choice-experiment/)
![R](https://img.shields.io/badge/R-4.3-276DC3?logo=r&logoColor=white)
![mlogit](https://img.shields.io/badge/mlogit-discrete%20choice-blue)
![caret](https://img.shields.io/badge/caret-ML-orange)

> **[→ Lee el reporte completo renderizado](https://jsanchez-ds.github.io/credit-choice-experiment/)** — cada gráfico, tabla y output del modelo, sin requerir instalación de R.

---

## Pregunta de investigación

> **¿La forma en que se despliega visualmente la información de crédito en avisos digitales cambia qué ofertas eligen los consumidores? ¿Y cambia la *consistencia* de esas elecciones vs. una baseline donde las mismas ofertas se mostraron sin material publicitario?**

La pregunta de política detrás: ¿deberían los reguladores forzar a los anunciantes de crédito a mostrar información financiera clave (CAE, costo total, valor de la cuota) de manera más prominente y estandarizada?

---

## Diseño experimental

Los participantes fueron asignados aleatoriamente a una de **cuatro condiciones**:

| Grupo | Descripción |
|---|---|
| **GC (Control)** | Layout típico usado hoy en el mercado de crédito chileno |
| **T1** | Se enfatiza visualmente el costo total del crédito (CTC) |
| **T2** | Se enfatizan tanto el costo total (CTC) como el valor de la cuota (VC) |
| **T3** | Toda la información financiera se enfatiza vs. otros elementos visuales |

Cada participante hizo **6 elecciones binarias** a través de tres macroescenarios (compra de TV / vacaciones / gasto médico inesperado). Sus elecciones en esta ola (ola 2) se comparan con una ola anterior (ola 1) donde las mismas ofertas se presentaron como tablas financieras planas — proporcionando una medida de **consistencia** within-subject limpia.

---

## Dataset

El Rmd lee desde `data/sample.csv` — una muestra **completamente sintética** de ~265 filas (≈132 elecciones binarias, 22 participantes simulados) generada para:

- Respetar el schema original columna por columna
- Coincidir con las distribuciones marginales de `condicion_experimental` y `macroescenario`
- Reproducir la mecánica básica de que los participantes tienden a elegir la alternativa con menor costo total (CTC)

El dataset original de 8,000 filas **no se redistribuye** porque fue entregado bajo los términos de un curso académico. Ver [`data/README.md`](data/README.md) para el schema completo.

---

## Metodología

### 1. EDA
- Demografía (género, alfabetización financiera, liquidez mensual)
- Tasas de elección a través de los cuatro atributos de oferta
- Consistencia vs. ola 1 por grupo experimental
- Errores de comprensión (recall de CTC / VC / CAE) por grupo experimental

### 2. Modelos de elección discreta (`mlogit`)
| Modelo | Tipo | Notas |
|---|---|---|
| `modelp1` | Logit condicional | Baseline: solo `CAE + CantCuotas` como atributos alternativa-específicos |
| `modelp2` | Logit condicional + interacciones | Agrega interacciones a nivel participante: `mujer × T3`, `lit_fin × T3`, `estima_bien × T3`, efectos fijos de escenario |
| `modelp3` | **Logit mixto** | Misma spec + coeficientes aleatorios con correlación, capturando **heterogeneidad no observada** entre participantes |

### 3. Comparación ML (caret)
Comparación predictiva sobre split test hold-out:
- **CART** (`rpart`)
- **SVM lineal** (`svmLinear`)
- **KNN** (`knn`)
- **Random Forest** (`rf`)

Modelos comparados vía `resamples()` sobre accuracy y Kappa.

---

## Hallazgos principales

1. **Los atributos de la oferta importan más.** A través de todos los modelos, los predictores más fuertes de elección son la **CAE** y el **número de cuotas** — el tratamiento visual es un efecto de segundo orden comparado con los números financieros de base.
2. **Los logits condicionales (modelp1, modelp2) no encontraron efecto T3 significativo** sobre la elección. El análisis discreto estándar sugiere que enfatizar la información financiera *no* cambia significativamente qué oferta elige la gente.
3. **El logit mixto (modelp3) cuenta otra historia.** Una vez que permitimos heterogeneidad no observada entre participantes, T3 *sí* muestra un efecto significativo sobre la probabilidad de elegir la alternativa izquierda, sugiriendo que los logits simples estaban enmascarando respuestas reales pero heterogéneas.
4. **Ninguna interacción con demografía es significativa.** Ser mujer, tener mayor alfabetización financiera, o estimar costos correctamente no modula significativamente el efecto T3 — al menos no en este tamaño de muestra.
5. **Implicación de política**: la evidencia es **inconclusa** pero sugiere que un estándar mínimo tipo T3 es defendible — garantiza que todos los usuarios vean la información relevante al mismo peso visual, aunque el efecto conductual promedio sea pequeño.
6. **Predicción**: Random Forest dio la mejor accuracy test-set entre los modelos ML, pero logit / logit mixto siguen siendo preferibles cuando el objetivo es *interpretar* el rol de cada variable.

---

## Stack técnico

`R` `mlogit` `caret` `rpart` `kernlab` `randomForest` `ggplot2` `dplyr` `kableExtra`

---

## Estructura del proyecto

```
credit-choice-experiment/
├── README.md
├── analysis.Rmd                    # Análisis completo (EDA + mlogit + ML)
├── data/
│   ├── README.md                   # Referencia de schema
│   └── sample.csv                  # Muestra sintética de 265 filas
└── .github/workflows/
    └── render.yml                  # CI: renderiza Rmd → HTML → Pages
```

---

## Cómo reproducir

### Opción A — Lee el reporte renderizado (sin instalar nada)

Abre <https://jsanchez-ds.github.io/credit-choice-experiment/>. El CI reconstruye el reporte en cada push a `main`.

### Opción B — Correr localmente

```r
install.packages(c(
  "rmarkdown", "knitr", "tidyverse", "dplyr", "readr", "ggplot2",
  "gridExtra", "ggcorrplot", "psych", "mlogit", "fixest", "glmnet",
  "caret", "kernlab", "earth", "randomForest", "kableExtra",
  "stargazer", "modelsummary", "gt"
))

rmarkdown::render("analysis.Rmd", output_dir = "docs", output_file = "index.html")
```

El Rmd lee `data/sample.csv` del repo, así que no hace falta descargar datos.

---

## Contexto

Originalmente desarrollado para **IN5162 — Marketing Engineering**, dictado por el Prof. Marcel Goic en la **Universidad de Chile (Ingeniería Industrial, 2023)**. Reformulado y reescrito en inglés para presentación de portfolio.

---

## Autor

**Jonathan Sánchez**
- GitHub: [@jsanchez-ds](https://github.com/jsanchez-ds)
- Universidad de Chile — Ingeniería Civil Industrial
