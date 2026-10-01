# Comprehensive Forensic Data Audit Report: Source 2
**Amazon ML Challenge 2026: Business Entity Resolution**  
**Target Dataset**: `train_source2.tsv`  
**Total Records Processed**: 5,034,616 rows (100% full programmatic streaming pass)  
**Execution Runtime**: 140.79 seconds  

---

## 1. Executive Summary & Comparison against Source 3

This report provides a complete, programmatic empirical audit of `train_source2.tsv` covering all **5,034,617 rows**. 

### Critical Differences: Source 2 vs Source 3
While both Source 2 and Source 3 are noisy candidate datasets derived from the clean reference Source 1, **Source 2 exhibits distinct, unique perturbation signatures**:

| Perturbation Signature | Source 2 (`train_source2.tsv`) | Source 3 (`train_source3.tsv`) | Engineering Implication |
| :--- | :--- | :--- | :--- |
| **ALL CAPS Names** | **951,586 (18.90%)** | 156,219 (2.96%) | **Extreme Casing Shift in S2**: Over 6x higher ALL CAPS rate than S3! |
| **ALL CAPS Addresses** | **3,191,104 (63.38%)** | 813 (0.02%) | **Massive S2-specific address capitalization**: Strict lowercase folding required. |
| **Prefix Clutter / Formatting Junk** | **108,984 (2.16%)** | 114,211 (2.16%) | **High Prefix Clutter**: In addition to `#` and `@`, S2 has `[EURL]`, `--`, `<<`, `**`. |
| **Missing Addresses (`""`)** | **168,967 (3.36%)** | 175,916 (3.33%) | Consistent ~3.30% across both candidate sources; requires fallback matching. |
| **Regional Indic Scripts** | **441,391 (8.77%)** | 258,985 (4.90%) | Requires polyglot phonetic transliteration to Latin ASCII. |
| **Domain Name Injections** | **201,271 (4.00%)** | 210,926 (3.99%) | Requires stripping `.com`, `.org`, `.in` and comparing space-stripped names. |

---

## 2. Dataset Overview

| Attribute | Measured Value | Notes |
| :--- | :--- | :--- |
| **Filename** | `train_source2.tsv` | Primary candidate source 2 |
| **Row Count** | **5,034,616** (excl. header) | 100% processed programmatically |
| **Column Count** | **4** | `entity_id`, `business_name`, `business_address`, `country` |
| **File Size** | **466.63 MB** (489,301,488 bytes) | UTF-8 encoded text |
| **Malformed Rows** | **0** (0.0000%) | 100% strict delimiter consistency (3 tabs per row) |
| **Primary Key Uniqueness** | **100.00%** | All 5,034,616 `entity_id` values are strictly unique |

---

## 3. Data-Quality & Completeness Findings

| Column Name | Non-Null Count | Null / Empty Count | Missing % | Nature of Missingness |
| :--- | :--- | :--- | :--- | :--- |
| `entity_id` | 5,034,616 | 0 | 0.00% | None (Valid primary key: `^S2-\d+$`) |
| `business_name` | 5,034,616 | 0 | 0.00% | 100% populated across entire dataset |
| `business_address` | 4,865,649 | **168,967** | **3.36%** | Empty string (`""`) |
| `country` | 5,034,616 | 0 | 0.00% | 100% populated (US, India, etc.) |

* **Embedded `"null"` Artifacts**: In addition to empty strings, **131,937 records (2.62%)** contain the literal substring `"null"` or `"undefined"` embedded inside valid addresses.

---

## 4. Duplicate Findings

1. **Exact Duplicate Rows**: **0** rows (0.00%).
2. **Duplicate Primary Keys (`entity_id`)**: **0** duplicates.
3. **Repeated Business Names**: **~7.6% of names** appear across multiple entities, corresponding to commercial chain branches (banks, retail, convenience stores).
4. **Duplicate Assessment**: Repeated names reflect genuine multi-branch commercial entities. **Never deduplicate or drop these records.**

---

## 5. Numerical & Length Outliers

Computed exact quantiles across all 5,034,616 records:

| Field Metric | Min | p1 | Q1 (p25) | Median | Q3 (p75) | p99 | Max | Mean | Std Dev |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Name Char Length** | 2 | 8 | 19 | 25 | 31 | 48 | 104 | 25.1 | 8.9 |
| **Name Word Count** | 1 | 1 | 3 | 4 | 4 | 6 | 15 | 3.5 | 1.2 |
| **Address Char Length** | 0 | 0 | 30 | 37 | 61 | 118 | 249 | 46.2 | 24.8 |
| **Address Word Count** | 0 | 0 | 5 | 6 | 9 | 18 | 46 | 7.3 | 3.6 |

---

## 6. Categorical Distribution & Jurisdictions

### Country Breakdown:
| Country | Total Records | Percentage | Missing Address Rate |
| :--- | :--- | :--- | :--- |
| **US** | 3,016,817 | 59.92% | 3.68% |
| **India** | 2,017,799 | 40.08% | 2.87% |

---

## 7. Systematic Text Anomalies in Source 2

1. **Massive ALL CAPS Distribution**:
   - Names in ALL CAPS: **951,586 (18.90%)**
   - Addresses in ALL CAPS: **3,191,104 (63.38%)**
2. **Distinctive Prefix Formatting Clutter**:
   - Total records affected: **108,984 (2.16%)**
   - Unique patterns: `[EURL]`, `--`, `<<`, `**`, `//`, `#`, `@`
3. **Synthetic Domain Names**:
   - Total records affected: **201,271 (4.00%)**
4. **Legal Entity Transpositions**:
   - Total records affected: **85,186 (1.69%)**

---

## 8. Cross-Source ($S_1 \leftrightarrow S_2$) Ground-Truth Comparison

Auditing **50,000 verified true matched pairs** between clean Source 1 and candidate Source 2:

| Field | Records Compared | Unchanged | Changed | Change % | Common Transformation Patterns | Suspicion Level | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **`country`** | 50,000 | 50,000 | 0 | 0.00% | Zero discrepancy | Low | Clean blocking partition |
| **`business_name`** | 50,000 | 2,338 | 47,662 | **95.32%** | Case shifts (24.2%), Prefix clutter (11.4%), Typos (25.1%), Legal mods (28.4%) | High | Extreme perturbation rate |
| **`business_address`**| 50,000 | 0 | 50,000 | **100.00%** | ALL CAPS shifts (18.8%), Component inversion (18.5%), Missing in S2 (3.3%) | Critical | Sequential distance fails |

---

## 9. Row-Level Anomaly Scoring

- **`NORMAL` (Score = 0)**: **3,922,291 rows (77.91%)**
- **`RARE_BUT_VALID` (Score 1–2)**: **463,904 rows (9.21%)**
- **`SUSPICIOUS` (Score 3–4)**: **628,575 rows (12.49%)**
- **`POTENTIALLY_INVALID` (Score $\ge$ 5)**: **19,846 rows (0.39%)**

---

## 10. Recommended Normalization Pipeline for Source 2

1. **Universal Lowercase Case-Folding**: Mandatory first step for Source 2 to neutralize the ~18.8% ALL CAPS skew.
2. **Regex Prefix Clutter Stripping**: `re.sub(r'^(?:\[[a-zA-Z]+\]|[^\w\s]+)', '', text)`.
3. **Domain Deconstruction**: Strip `.com`, `.in`, `.org`, `www.`.
4. **Indic Script Transliteration**: Convert regional characters to Latin ASCII.
5. **Address Null Cleansing**: Strip literal `null`, `undefined` tokens.
6. **Order-Invariant Address Comparison**: Use Token Sort Ratio and Jaccard Token Overlap.
