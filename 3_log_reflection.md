# 3_log_reflection.md — Cleaning Log and Reflection

**Assignment:** Assignment 2 — The Dirty Ledger  
**Dataset:** `teaching_prices_messy.csv`

## 1. Cleaning log

| Check / item | Before | Action | After / evidence | Decision |
|---|---:|---|---|---|
| Rows | 97 | No row deletion | 97 | KEEP all rows pending review |
| Columns | 6 | No column deletion | 6 | KEEP all columns |
| Original missing cells | 1 | Counted in the raw data | 1, in `price` | FLAG; do not impute automatically |
| Exact duplicate rows | 1 duplicate occurrence | Checked all duplicate occurrences | Rows 8 and 96 are identical | FLAG for review before removal |
| Duplicate date-item-city keys (original labels) | 2 records | Checked repeated keys | Rows 8 and 96 share the same key | FLAG for review |
| Duplicate keys after trimming spaces and ignoring case in item names | 4 records | Checked normalized item keys without changing the original labels | The two `Staple A` rows and the `Oil B` / `oil b` pair on 2026-02-05 in City 1 | FLAG both pairs; inspect before deciding |
| Price values | 1 missing; remaining values numeric after removing thousands separators | Removed commas in a separate conversion column and converted to numeric | 96 numeric values; 1 original missing value; 0 conversion failures | MODIFY conversion method; do not impute |
| Non-positive prices | 0 | Checked converted numeric values | 0 | No correction indicated |
| Dates | 12 distinct values; parseable | Parsed in a separate working column | 0 date conversion failures | MODIFY type in working copy only |
| Item labels | 5 exact labels | Compared after trimming and ignoring case | 4 normalized labels; `Oil B` and `oil b` differ only in capitalization | FLAG for inspection; do not recode automatically |
| Rows per exact item label | Four labels have 24 rows; `oil b` has 1 | Counted item labels | `Oil B` has 24 and `oil b` has 1 | FLAG the inconsistent label |
| Cities | 2 unique cities | No recoding | 2 | KEEP |
| Units | 1 consistent unit label | No standardization required | `PKR per kg (illustrative)` | KEEP |
| Data label | 1 consistent label | Preserved source context | `ILLUSTRATIVE TEACHING DATA - NOT ACTUAL PBS OBSERVATIONS` | KEEP |

## 2. Price and duplicate checks

The price value `1,250` is a number written with a thousands separator. Removing the comma in a separate working column allows it to be converted to numeric without treating it as invalid. There are therefore **0 non-numeric price conversion failures**, not 1.

There is one original missing price, at row 5 (index 5), for `Pulse C` in City 2 on 2026-01-01. It remains missing; no value is imputed.

The exact duplicate occurs at rows 8 and 96: both contain the same `Staple A` observation for City 1 on 2026-01-08, with a price of 99.5. The notebook flags both records and does not delete either automatically.

The `Oil B` / `oil b` records also need attention. The lowercase label occurs on 2026-02-05 in City 1, where an `Oil B` record already exists. Their item labels differ only in capitalization, so they form a repeated date-item-city key when item names are compared without case sensitivity. The lowercase record has a price of 108.5, while the corresponding `Oil B` record has a price of 318. This difference is a reason to inspect the records, not sufficient evidence to change or delete either value.

The maximum price is 9,999, for `Sweetener D` in City 1 on 2026-01-22. It is unusually high compared with the median price of 200. It is flagged for review, but it is not automatically deleted or replaced.

## 3. AI-code verdicts

| Suggested operation | Verdict | Reason |
|---|---|---|
| Load CSV using pandas | KEEP | Necessary for inspection |
| Copy raw data before cleaning | KEEP | Protects the original |
| Parse dates | MODIFY | Parse in a working copy and count failures |
| Convert prices to numeric | MODIFY | Remove the thousands separator in a working column, then count conversion failures |
| Drop all rows with missing values | REJECT | One missing price exists; blanket deletion is not justified |
| Drop exact duplicates automatically | MODIFY | Inspect the duplicate records before deciding whether to remove one |
| Drop repeated date-item-city keys automatically | REJECT | Both the exact duplicate and case-insensitive item-label collision need inspection |
| Delete statistical outliers automatically | REJECT | The value 9,999 is unusual but is not proven to be incorrect |
| Fill missing prices with the mean | REJECT | No evidence supports inventing a replacement price |
| Standardize `Oil B` and `oil b` automatically | MODIFY | The labels differ only in capitalization, but the conflicting records should be checked first |
| Rewrite units or source labels | REJECT | The values are consistent and the source context must be preserved |
| Overwrite the original file | REJECT | The original must remain available |
| Compare row counts and missing counts | KEEP | Makes changes and possible record loss visible |

## 4. Before-and-after summary

- Rows: **97 before; 97 after**.
- Columns: **6 before; 6 after**.
- Original missing cells: **1**.
- Numeric prices after handling the comma in `1,250`: **96**.
- Price conversion failures: **0**.
- Exact duplicate rows: **1 duplicate occurrence**.
- Repeated date-item-city key records using original labels: **2**.
- Repeated date-item-city key records after case-insensitive item comparison: **4**.
- Non-positive prices: **0**.
- Unparseable dates: **0**.
- Rows removed: **0**.
- Price values imputed or changed: **0**.

The code keeps the original data unchanged and creates separate parsed date and numeric price columns for inspection.

## 5. Reflection

The checks show why it is important to inspect a dataset before cleaning it. The file contains one missing price, an exact duplicate, an item label that differs only in capitalization, and an unusually high price. These issues require attention, but they do not all justify automatic deletion or replacement.

One important detail is the price written as `1,250`. It is a valid number with a thousands separator, so the comma should be removed for numeric conversion in a separate working column. Treating it as an invalid value would incorrectly classify a valid price as a conversion failure.

The duplicate records also require careful treatment. Rows 8 and 96 are identical, while the `Oil B` and `oil b` records share the same date and city after ignoring capitalization. However, the latter pair has different prices. I would inspect these records before deciding whether to remove a duplicate or standardize a label.

The price of 9,999 is much higher than the median of 200, but an unusual value is not automatically an error. It should be checked against the source rather than deleted simply because it affects the average.

The dataset's label states that it is illustrative teaching data and not actual PBS observations. This label should be preserved, and the values should not be presented as official PBS prices or used to make claims about actual market behaviour.
