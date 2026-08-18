# Delivery Performance Data Analysis

Exploratory data analysis of a delivery operations dataset using Python, pandas, and SQL covering data quality checks, delivery time distributions, threshold-based filtering, delay reason analysis, time trends, and multi-table SQL analysis across orders, drivers, hubs, and vehicles.

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

Three supplementary tables provide additional detail, joined to the main dataset via SQLite:

| Table | Columns | Join key |
|---|---|---|
| `Drivers.csv` | DriverID, DriverName, Employment Type, Hire Date, Experience Years, Performance Rating | `DriverID` ↔ `Driver ID` |
| `Hubs.csv` | Hub ID, HubName, Hub Capacity | `HubName` ↔ `Hub Name` |
| `Vehicles.csv` | Purchase Date, Vehicle ID, Vehicle Model, Vehicle Status, Breakdown, Maintenance count Alert, Vehicle Code | `Vehicle Code` ↔ `Vehicle Name` |

> Note: the raw data files are not included in this repo. To run the notebooks, add your own CSVs and update the file paths in the first code cell of each.

## Notebooks

| Notebook | Description |
|---|---|
| [`01_Data_Exploration.ipynb`](./01_Data_Exploration.ipynb) | Initial exploration: data quality checks, delivery time stats, threshold-based breakdowns by hub/driver, and delay reason analysis |
| [`02_Data_Cleaning_and_Customer_Satisfaction.ipynb`](./02_Data_Cleaning_and_Customer_Satisfaction.ipynb) | Full data cleaning checklist (missing values, duplicates, date parsing, range checks), plus an investigation into how delivery time affects customer satisfaction |
| [`03_Time_Trends_and_KPI_Dashboard.ipynb`](./03_Time_Trends_and_KPI_Dashboard.ipynb) | Monthly performance trends over 24 months, plus a reusable operational KPI dashboard function broken down by hub and driver |
| [`04_SQL_Multi_Table_Analysis.ipynb`](./04_SQL_Multi_Table_Analysis.ipynb) | Integrates Drivers, Hubs, and Vehicles tables via SQLite; tests whether driver ratings, experience, and vehicle status/maintenance data actually predict delivery outcomes, using SQL joins and aggregations |

## Key Findings

- Of 27,979 total orders, 252 (0.9%) were cancelled and have no recorded delivery time. This is expected (a cancelled order was never delivered), not a data quality issue.
- Average delivery time is **35.78 hours**, while the median is **33.06 hours**. The gap indicates a right-skewed distribution, where a small number of very slow deliveries (120+ hours) pull the average upward.
- Average delivery time by hub is fairly consistent (35.2–36.5 hours across all six hubs), suggesting hub location alone isn't a major driver of delay.
- Reusable functions (`breakdown_by_group()`, `delay_reason_breakdown()`, `delivery_count_by_threshold()`) allow quick re-analysis across any time threshold, direction (under/over), or grouping column.
- The dataset passed a full cleaning checklist (no duplicates, no out-of-range values). The only missing data (252 rows) is fully explained by legitimate order cancellations.
- Delivery time and customer satisfaction show a weak overall correlation (-0.287), but a bucketed breakdown reveals a **sharp satisfaction "tipping point" at 3 days**. Satisfaction is stable under 72 hours (~4.3/5), then drops to 3.04 for 3-5 day deliveries, and falls to 1.78 for deliveries over 5 days.
- Performance is fairly stable across 24 months (Jan 2023 – Dec 2024), with a mild overall decline in 2024. **May–July 2024** stands out as the weakest stretch, with delivery time, on-time rate, and satisfaction all degrading simultaneously.
- **Austin Hub** ranks lowest overall not due to a slow average, but due to tail risk. The highest rate of 5+ day deliveries (0.76%) among all hubs, is a pattern the average alone would not have shown.
- **Fort Worth Hub** has the slowest average delivery time despite handling low volume; a standard deviation check confirmed this is a consistent (not outlier-driven) effect, though modest.
- Driver-level satisfaction differences are narrow (0.17-point spread, best to worst), the weakest signal found in the project. One driver (Karen Hernandez) stands out with both the slowest average delivery time and lowest satisfaction score, though a delay-reason investigation found no specific identifiable cause.
- Integrating driver and vehicle data via SQL revealed a consistent null result across four separate "quality" signals: `Performance Rating`, `Experience Years`, `Vehicle Status`, and `Maintenance count Alert` (found to be redundant with Vehicle Status). **None showed a meaningful relationship** with delivery time, satisfaction, or breakdown-delay rate. This suggests operational outcomes are driven by factors other than the driver/vehicle attributes captured in these tables.
- Vehicles currently flagged "Maintenance" showed a breakdown-delay rate (2.14%) nearly identical to "Active" vehicles (2.26%), counter to the intuitive expectation and a reminder to normalize by volume rather than compare raw counts.

## Tools & Concepts Used

- **pandas** — filtering, `.groupby()`, `.agg()`, handling missing data, merging/joining data sources
- **SQL / SQLite** — `JOIN`, `GROUP BY`, aggregate functions (`COUNT`, `AVG`, `ROUND`), multi-table relational analysis via `sqlite3` and `pd.read_sql()`
- **NumPy** — underlying numerical operations
- **Core Python** — `len()`, `sum()`, `round()`, `sorted()`, `enumerate()`, `zip()`, custom functions
- **matplotlib** — distribution visualization

## Next Steps

- Explore `Hubs.csv`'s `Hub Capacity` field against order volume per hub, to test whether hubs operating over/under capacity show different performance.
- Investigate whether `Order Date` timing could approximate vehicle status at time of delivery, rather than relying on the current status snapshot.
- Explore whether the May–July 2024 dip correlates with any external/seasonal factor.
