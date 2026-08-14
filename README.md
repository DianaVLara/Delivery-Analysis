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
| [`03_Time_Trends_and_KPI_Dashboard.ipynb`](./03_Time_Trends_and_KPI_Dashboard.ipynb) | Monthly performance trends over 24 months, plus a reusable operational KPI dashboard function broken down by hub and driver |

## Key Findings

- Of 27,979 total orders, 252 (0.9%) were cancelled and have no recorded delivery time. This is expected (a cancelled order was never delivered), not a data quality issue.
- Average delivery time is **35.78 hours**, while the median is **33.06 hours**. The gap indicates a right-skewed distribution, where a small number of very slow deliveries (120+ hours) pull the average upward.
- Average delivery time by hub is fairly consistent (35.2–36.5 hours across all six hubs), suggesting hub location alone isn't a major driver of delay.
- Reusable functions (`breakdown_by_group()`, `delay_reason_breakdown()`, `delivery_count_by_threshold()`) allow quick re-analysis across any time threshold, direction (under/over), or grouping column.
- The dataset passed a full cleaning checklist (no duplicates, no out-of-range values). The only missing data (252 rows) is fully explained by legitimate order cancellations.
- Delivery time and customer satisfaction show a weak overall correlation (-0.287), but a bucketed breakdown reveals a **sharp satisfaction "tipping point" at 3 days**. Satisfaction is stable under 72 hours (~4.3/5), then drops to 3.04 for 3-5 day deliveries, and falls to 1.78 for deliveries over 5 days.
- Performance is fairly stable across 24 months (Jan 2023 – Dec 2024), with a mild overall decline in 2024. **May–July 2024** stands out as the weakest stretch, with delivery time, on-time rate, and satisfaction all degrading simultaneously.
- **Austin Hub** ranks lowest overall not due to a slow average, but due to tail risk. The highest rate of 5+ day deliveries (0.76%) among all hubs.
- **Fort Worth Hub** has the slowest average delivery time despite handling low volume; a standard deviation check confirmed this is a consistent (not outlier-driven) effect, though modest. A lower-confidence finding than Austin's.
- Driver-level satisfaction differences are narrow (0.17-point spread, best to worst), the weakest signal found in the project. One driver (Karen Hernandez) stands out with both the slowest average delivery time and lowest satisfaction score, though a delay-reason investigation found no specific identifiable cause.

## Tools & Concepts Used

- **pandas** — filtering, `.groupby()`, `.agg()`, handling missing data
- **NumPy** — underlying numerical operations
- **Core Python** — `len()`, `sum()`, `round()`, `sorted()`, `enumerate()`, `zip()`, custom functions
- **matplotlib** — distribution visualization

## Next Steps

- Investigate route/geography-level data if available, to test whether individual driver performance differences (e.g. Karen Hernandez) are explained by factors not captured in delay reason data
- Extend the `kpi_dashboard()` function to a vehicle-type breakdown
- Explore whether the May–July 2024 dip correlates with any external/seasonal factor
