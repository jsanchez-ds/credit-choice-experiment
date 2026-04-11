# Data

The analysis uses `credit-choice.csv`, the merged choice + survey dataset from the experiment described in the main README.

The original dataset is **not redistributed** in this repository — it was provided under the terms of an academic course at Universidad de Chile (IN5162 — Marketing Engineering).

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
| `type_cons` | string | consistency_slider / consistency_table / dominated_choice |
| `macroescenario` | string | TV / Travel / Health |
| `monto_oferta` | int | Loan amount |
| `ola1.cuotas_optimas_*` | int | Wave-1 chosen # of installments at different amounts |
| `ola2.error_estimacion_*` | float | Wave-2 errors estimating CTC / VC |
| `ola2.error_recordacion_*` | float | Wave-2 recall errors for CTC / VC / CAE / # installments |
| `ola2.grado_consistencia` | int (0–4) | # of consistent choices vs wave 1 |
| `ola2.grado_corr.dominancia` | int (0–2) | # of times the dominant alternative was chosen |
| `ola2.est_mercado` | string | Market comparison perception |
| `dem.mujer` | int (0/1) | 1 if female |
| `dem.liquidez` | float | Declared monthly liquidity for financial expenses |
| `dem.lit_fin` | int (0–3) | Financial literacy score |
