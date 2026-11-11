# 1_attempt.md — Attempt Before AI

**Assignment:** Assignment 2 — The Dirty Ledger  
**Dataset:** `teaching_prices_messy.csv`

## 1. Problems to investigate

Before changing any values, I planned to investigate:

- Whether the columns have clear names and appropriate data types.
- Whether dates can be interpreted consistently.
- Whether prices are numeric and positive.
- Whether any cells are missing.
- Whether there are exact duplicate rows.
- Whether the combination of date, item, and city is duplicated.
- Whether item names, units, and data labels are consistent.
- Whether each item appears the expected number of times.
- Whether any unusually high or low prices need investigation.
- Whether the file contains actual observations or illustrative teaching data.

## 2. Planned decisions

| Potential issue | Planned decision | Reason |
|---|---|---|
| Missing values | Investigate first; do not automatically fill | Filling without evidence could invent prices or categories |
| Non-numeric prices | Check conversion failures and inspect affected records | A failed conversion should not silently be accepted |
| Duplicate rows | Check exact duplicates before removing anything | Similar-looking records are not necessarily duplicates |
| Repeated date-item-city combinations | Flag for inspection | A repeated key may indicate duplication or a data-entry issue |
| Invalid or unparseable dates | Flag; do not guess a replacement date | A guessed date would change the observation |
| Non-positive prices | Investigate and flag | A price should be positive in this teaching dataset |
| Inconsistent item names, units, or labels | Standardize only when the meaning is clearly identical | Different categories or units must not be combined |
| Unusual prices | Flag for review; do not delete only because a value looks unusual | An unusual value may be genuine |
| Source/data label | Preserve it | The label provides important context about the observations |
| Original file | Keep unchanged and work on a copy | This preserves the original data |
