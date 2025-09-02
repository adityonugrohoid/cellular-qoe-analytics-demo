# cellular-qoe-analytics-demo

**Hands-on demo for application-layer QoE analytics on cellular/Wi-Fi networks — from synthetic data to hotspots, alerting, and ML.**

---

## Overview

End-to-end, notebook-driven workflow for Quality of Experience (QoE) analysis. Generate realistic session data, explore KPIs by operator and RAT, surface hotspots, set up alerting (with optional Telegram push), and train models to predict page load time (PLT).

---

## What’s inside

- **Synthetic sessions (100k+ by default):** 4G / 5G / Wi-Fi with operator, band/channel, radio KPIs, `rtt_ms`, `app_size_kb`, and QoE KPIs (PLT, buffering, startup delay).
- **Physics-based QoE mapping:** smooth radio→QoE, device/page weighting, reproducible randomness.
- **Hotspot workflow:** operator × RAT rollups, percentile stats, drill-downs by cell/PCI/TAC/band.
- **Correlation suite:** radio/observable heatmaps, categorical associations (η, Cramér’s V).
- **Alerting path:** daily SLO rollups, CSV export, optional Telegram integration.
- **Modeling:** time-aware splits, OHE+scaling pipelines, multiple regressors, saved artifacts.
- **Flexible trend analysis:** interactive time series slicing by device, network type, and operator.

---

## Data

- **Self-contained synthetic data:** generated via `00_generate_synthetic_data.ipynb`.
- **Schema (core columns):**  
  `timestamp, country, device, network_type, operator, band, channel_number, rsrp_dbm, rsrq_db, sinr_db, rtt_ms, app_size_kb, page_load_time_ms, buffering_ratio, startup_delay_ms, pci, tac, cell_id, session_id`

> Parquet output if available, CSV fallback is automatic.

---

## Notebook pipeline

- **00_generate_synthetic_data.ipynb**  
  Simulate realistic session-level data across regions/operators/RATs. Outputs `synthetic_qoe_sessions.csv`.
- **01_data_overview_and_cleaning.ipynb**  
  Basic QC, NaN drop on KPIs, dtype optimization, and write `synthetic_qoe_sessions_clean.[parquet|csv]`.
- **02_kpi_exploration_and_aggregation.ipynb**  
  - Print data date range.  
  - `operator × RAT` rollups: p50/p95/p99 PLT, p95 buffering/startup, and counts.  
  - Select hotspot, drill-down by cell/PCI/TAC/Band×Channel.  
  - PLT relationships: regplots (PLT vs RSRP/RSRQ/SINR/RTT/App Size), percentile zoom.  
  - Daily trends by RAT, overall/hotspot.  
  - *(Optional)*: Correlations & category effects (Pearson, η, Cramér’s V).  
  - Writes artifacts to `./artefacts/`.
- **03_alerting_and_automation.ipynb**  
  Daily rollups → threshold checks → `network_qoe_alerts.csv`. Optional Telegram send via Bot API.
- **04_ml_modeling.ipynb**  
  Predict `page_load_time_ms` using radio, device, operator, RAT, and context.  
  Time-aware split, Linear/Tree/RF/GBR models, permutation importance, model saving.
- **05_time_series_trend_analysis.ipynb**  
  Interactive time series visualization of KPIs. Filter by device, network type, and operator using dropdowns.  
  Slice trends for any combination to spot patterns and outliers.

---

## Setup (Conda `environment.yml`)

```bash
# Create the environment from environment.yml
conda env create -f environment.yml

# Activate it
conda activate qoe-demo
