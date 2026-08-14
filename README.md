# Delivery Performance Data Analysis

Exploratory data analysis of a delivery operations dataset using Python and pandas — covering data quality checks, delivery time distributions, threshold-based filtering, and delay reason analysis.

## Project Overview

This project explores a dataset of ~28,000 delivery orders to answer questions like:

- What's the average, fastest, and slowest delivery time?
- How many deliveries are completed within (or exceed) specific time thresholds?
- Which hubs and drivers perform best/worst against those thresholds?
- What are the most common reasons for delivery delays, and do damaged shipments play a role?
- Is delivery time evenly distributed, or skewed by outliers?

## Dataset

The dataset contains 15 columns covering order details, delivery timing, and outcomes:

`Order ID`, `Actual Delivery Date`, `Delay Reason`, `Driver ID`, `Driver Name`, `Hub Name`, `Is Delayed`, `Is On Time`, `Order Date`, `Order Status`, `Vehicle Name`, `Vehicle Type`, `Customer Satisfaction Score`, `Delivery Time Hours`, `Hub Processing Time Hours`

> Note: the raw data file is not included in this repo. To run the notebook, add your own CSV and update the file path in the first code cell.

## Notebooks

| Notebook | Description |
|---|---|
| [`01_Data_Exploration.ipynb`](./01_Data_Exploration.ipynb) | Initial exploration: data quality checks, delivery time stats, threshold-based breakdowns by hub/driver, and delay reason analysis |
| [`02_Data_Cleaning_and_Customer_Satisfaction.ipynb`](./02_Data_Cleaning_and_Customer_Satisfaction.ipynb) | Full data cleaning checklist (missing values, duplicates, date parsing, range checks), plus an investigation into how delivery time affects customer satisfaction |

## Key Findings

- Of 27,979 total orders, 252 (0.9%) were cancelled and have no recorded delivery time — this is expected (a cancelled order was never delivered), not a data quality issue.
- Average delivery time is **35.78 hours**, while the median is **33.06 hours** — the gap indicates a right-skewed distribution, where a small number of very slow deliveries (120+ hours) pull the average upward.
- Average delivery time by hub is fairly consistent (35.2–36.5 hours across all six hubs), suggesting hub location alone isn't a major driver of delay.
- Reusable functions (`breakdown_by_group()`, `delay_reason_breakdown()`, `delivery_count_by_threshold()`) allow quick re-analysis across any time threshold, direction (under/over), or grouping column.
- The dataset passed a full cleaning checklist (no duplicates, no out-of-range values) — the only missing data (252 rows) is fully explained by legitimate order cancellations.
- Delivery time and customer satisfaction show a weak overall correlation (-0.287), but a bucketed breakdown reveals a **sharp satisfaction "tipping point" at 3 days** — satisfaction is stable under 72 hours (~4.3/5), then drops to 3.04 for 3-5 day deliveries, and falls to 1.78 for deliveries over 5 days.

## Tools & Concepts Used

- **pandas** — filtering, `.groupby()`, `.agg()`, handling missing data
- **NumPy** — underlying numerical operations
- **Core Python** — `len()`, `sum()`, `round()`, `sorted()`, `enumerate()`, `zip()`, custom functions
- **matplotlib** — distribution visualization

## Next Steps

- Investigate what's driving deliveries past the 3-day satisfaction tipping point (which hubs/drivers/delay reasons are overrepresented in that group)
- Analyze delivery time trends over time using the now-cleaned `Order Date` column
- Break down on-time performance (`Is Delayed` / `Is On Time`) by hub, driver, and vehicle type
