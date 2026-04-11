# Data

## Original dataset

The original analysis used `credit-choice.csv` (~8000 rows in long format,
two rows per binary choice). **It is not redistributed in this repository**
because it was provided under the terms of an academic course at Universidad
de Chile (IN5162 — Marketing Engineering).

## Synthetic sample (`sample.csv`)

This repository ships a **fully synthetic** sample at `data/sample.csv`
(~265 rows = ~132 binary choices, 22 simulated participants × 6 choices × 2
alternatives). It is generated to:

- Respect the original schema column-by-column
- Match the marginal distributions of `condicion_experimental` and
  `macroescenario`
- Reproduce the basic mechanic that participants tend to pick the alternative
  with lower total cost (CTC)

It is designed so that the analysis Rmd runs end-to-end against `sample.csv`
and produces qualitatively similar (but smaller-N) results. **It contains no
real participant records.**

## Schema

Long format: each binary choice contributes two rows (one per alternative).

| Column | Type | Description |
|---|---|---|
| `choice_id` | int | Choice id |
| `numero_eleccion` | int (1–6) | Choice number for that participant |
| `idp` | int | Participant id |
| `feat.CantCuotas` | int | # of installments shown |
| `feat.CAE` | float | APR (Carga Anual Equivalente) shown |
| `feat.CTC` | float | Total cost of credit shown |
| `feat.VC` | float | Installment value shown |
| `choice` | int (0/1) | 1 if this alternative was chosen |
| `option` | int (1/2) | Position (right / left) |
| `condicion_experimental` | string | GC / T1 / T2 / T3 |
| `type_cons` | string | consistency type |
| `macroescenario` | string | TV / Travel / Health |
| `monto_oferta` | int | Loan amount |
| `ola1.cuotas_optimas_*` | int | Wave-1 chosen # of installments |
| `ola2.error_*` | float | Wave-2 estimation/recall errors |
| `ola2.grado_consistencia` | int (0–4) | Consistent choices vs wave 1 |
| `dem.mujer` | int (0/1) | 1 if female |
| `dem.liquidez` | float | Declared monthly liquidity |
| `dem.lit_fin` | int (0–3) | Financial literacy score |
