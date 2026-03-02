# Coffee Market Data Cleaning & Pipeline (Python → MySQL)

## Overview
This project builds a reproducible cleaning and loading pipeline for multi-source coffee market data (prices, production, imports, exports, consumption). The output is a set of tables loaded into MySQL for future SQL analytics and BI visualisation.

## Objectives
- Standardise country naming and schema across multiple datasets
- Apply transparent data quality checks (schema, types, completeness, uniqueness, validity)
- Produce analytics-ready tables in MySQL for downstream querying and dashboarding

## Data Sources 
- Source: Kaggle and coffee index websites
- Green coffee prices
- Production
- Country Imports
- Country Exports
- Domestic consumption
- Importers consumption

## Pipeline
1. Load raw CSVs into pandas dataframes
2. Clean and standardise (country mapping, types, dates, reshaping)  
3. Data quality checks (nulls, duplicates, validity, consistency)  
4. Load curated tables into MySQL  

## Key Cleaning Decisions
- Country names standardised to common ISO-friendly naming (e.g., “Bolivia Plurinational State Of” → “Bolivia”)
- No Null values detected
- Provisional final period columns removed when coverage is incomplete

## How to Run
1. Install requirements
2. Configure MySQL credentials
3. Run scripts in `/src` in numeric order

## Outputs
- Curated CSV/parquet tables in `data/curated/`
- MySQL tables: production, import, export, consumption, prices
- SQL QA checks and marts in `/sql`
