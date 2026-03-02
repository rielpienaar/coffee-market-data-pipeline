# Data Dictionary — Coffee Market Pipeline

This data dictionary describes the cleaned, standardised tables produced by the Python cleaning pipeline and loaded into MySQL for analysis.

## Conventions
- `country` is standardised to consistent, BI-friendly names (e.g., *“Bolivia Plurinational State Of” → “Bolivia”*).
- Tables are stored in **long format** where applicable: one row per entity per `year` (and `coffee_type` where relevant).
- `value` fields are numeric measures. Missing values are retained where they represent “not reported” / “not applicable”.

> **Units note:** Many coffee market datasets (e.g., ICO-style tables) are commonly reported in *thousand 60kg bags*. If your source uses different units, update the “Units” lines below to match your file documentation.

---

## Table: `prices`
Green coffee price time series.

| Column | Type | Description | Example |
|---|---|---|---|
| `date` | DATE / DATETIME | Price date (day-grain). Stored without time-of-day significance. | `2025-01-15` |
| `value` | FLOAT | Green coffee price for the given date. | `184.23` |

**Primary key:** `date`  
**Notes:** No missing values expected after cleaning; `date` should be unique.

---

## Table: `production`
Annual coffee production by country and coffee type.

| Column | Type | Description | Example |
|---|---|---|---|
| `country` | VARCHAR | Producing country (standardised). | `Brazil` |
| `year` | INT | Reporting year (or crop year converted to year). | `2020` |
| `coffee_type` | VARCHAR | Coffee type (typically `Arabica`, `Robusta`, `Total`). | `Arabica` |
| `value` | FLOAT | Production volume for the given country/year/type. | `43000` |

**Primary key:** (`country`, `year`, `coffee_type`)  
**Units:** *(commonly)* thousand 60kg bags  
**Notes:** `value` should be non-negative; null may indicate not reported / not applicable.

---

## Table: `export`
Annual coffee exports by country.

| Column | Type | Description | Example |
|---|---|---|---|
| `country` | VARCHAR | Exporting country (standardised). | `Colombia` |
| `year` | INT | Reporting year. | `2020` |
| `value` | FLOAT | Export volume for the given country/year. | `12000` |

**Primary key:** (`country`, `year`)  
**Units:** *(commonly)* thousand 60kg bags  
**Notes:** `value` should be non-negative. In some cases exports may exceed production due to stock drawdown and/or re-exports.

---

## Table: `import`
Annual coffee imports by country.

| Column | Type | Description | Example |
|---|---|---|---|
| `country` | VARCHAR | Importing country (standardised). | `United Kingdom` |
| `year` | INT | Reporting year. | `2020` |
| `value` | FLOAT | Import volume for the given country/year. | `3200` |

**Primary key:** (`country`, `year`)  
**Units:** *(commonly)* thousand 60kg bags  
**Notes:** `value` should be non-negative.

---

## Table: `consumption_domestic`
Annual domestic coffee consumption by country and coffee type.

| Column | Type | Description | Example |
|---|---|---|---|
| `country` | VARCHAR | Consuming country (standardised). | `Ethiopia` |
| `year` | INT | Reporting year. | `2020` |
| `coffee_type` | VARCHAR | Coffee type (typically `Arabica`, `Robusta`, `Total`). | `Total` |
| `value` | FLOAT | Domestic consumption volume. | `2500` |

**Primary key:** (`country`, `year`, `coffee_type`)  
**Units:** *(commonly)* thousand 60kg bags  
**Notes:** Nulls may indicate not reported / not applicable.

---

## Table: `consumption_importers`
Annual consumption for importing countries (or importer-side consumption), by country (and coffee type if present in your dataset).

| Column | Type | Description | Example |
|---|---|---|---|
| `country` | VARCHAR | Importing/consuming country (standardised). | `Germany` |
| `year` | INT | Reporting year. | `2020` |
| `coffee_type` | VARCHAR *(optional)* | Coffee type if present (`Arabica`, `Robusta`, `Total`). | `Total` |
| `value` | FLOAT | Consumption volume. | `9000` |

**Primary key:**  
- If `coffee_type` exists: (`country`, `year`, `coffee_type`)  
- If `coffee_type` does not exist: (`country`, `year`)  

**Units:** *(commonly)* thousand 60kg bags  
**Notes:** If this table is already “total consumption” only, `coffee_type` may be absent.

---

## Derived / Standardised fields (used across tables)
These fields may be created during cleaning and used for joining and analysis:

| Field | Description |
|---|---|
| `country` | Standardised country name used across all tables for joins and BI mapping. |
| `year` | Integer year used as the primary temporal join key across annual datasets. |
| `coffee_type` | Standardised category enabling Arabica/Robusta/Total comparisons where applicable. |
| `value` | Numeric measure (volumes or prices depending on the table). |

---

## Join keys (how tables connect)
Typical join patterns:
- Annual tables join on `country` + `year`
- Coffee type tables additionally join on `coffee_type`
- Price time series (`prices`) can be related to annual tables by deriving `year = YEAR(date)` if needed.
