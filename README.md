# NYC-Yellow-Taxi-Operations-Intelligence
Exploratory Data Analysis of Urban Mobility - May 2026


## Project Overview

This project analyzes NYC Yellow Taxi trip data for **May 2026** to understand urban mobility patterns, revenue performance, passenger behavior, geographic demand, and operational efficiency.

The analysis combines **Python, SQL, and Tableau** to transform raw trip-level data into actionable business insights.

The project follows an end-to-end analytics workflow:

**Raw Data → Data Quality → Cleaning → Feature Engineering → EDA → SQL Analytics → Tableau → Business Recommendations**

---

## Business Problem

Taxi operators generate millions of trips across New York City, creating a large volume of operational and commercial data.

However, raw trip records contain challenges such as missing values, invalid timestamps, negative fares, zero-distance trips, and extreme outliers. The data also contains numeric pickup and drop-off location IDs that require geographic enrichment.

The objective is to determine:

* When taxi demand is highest
* Where demand is concentrated
* What drives revenue
* How passenger behavior varies
* How trip efficiency changes throughout the day
* How airport trips compare with other trips
* Which locations and routes are commercially important
* Where operational opportunities may exist

---

## Project Objectives
*  Demand & Utilization Analysis: Identify peak pickup/drop-off hours, day-of-week trends, Weekday vs weekend, and rush-hour volume surges.
  
*  Revenue Optimization: Evaluate fare structures, tip behavior across payment methods, toll contributions, and revenue-per-mile metrics.

*  Geographic Insights: Highlight top-performing pickup and drop-off boroughs/zones, with a focus on high-yield locations like airports (JFK, LaGuardia).

*  Operational Efficiency: Settle baseline metrics for trip duration, speed categories, and distance distributions to minimize idle time and optimize dispatch strategies.

*  Data Quality: Assess missing values, invalid timestamps, negative fares, and extreme outliers.

---

# Dataset
The analysis ingests the official NYC Taxi & Limousine Commission (TLC) trip records and zone lookup metadata:

## Primary Dataset

**NYC Yellow Taxi Trip Records — May 2026**

The trip dataset contains approximately **4.09 million raw records** and **20 columns** before cleaning.

Key fields include:

| Column                | Description                |
| --------------------- | -------------------------- |
| VendorID              | Technology provider/vendor |
| tpep_pickup_datetime  | Trip pickup timestamp      |
| tpep_dropoff_datetime | Trip drop-off timestamp    |
| passenger_count       | Number of passengers       |
| trip_distance         | Trip distance in miles     |
| RatecodeID            | Rate code                  |
| PULocationID          | Pickup location ID         |
| DOLocationID          | Drop-off location ID       |
| payment_type          | Payment method             |
| fare_amount           | Metered fare               |
| tip_amount            | Tip amount                 |
| total_amount          | Total trip amount          |
| congestion_surcharge  | Congestion surcharge       |
| Airport_fee           | Airport fee                |
| cbd_congestion_fee    | CBD congestion fee         |

The raw data contains missing values in fields including passenger count, rate code, congestion surcharge, and airport fee.

---

## Geographic Dataset

**Taxi Zone Lookup**

The lookup table maps `LocationID` to:

* Borough
* Zone
* Service zone

The geographic metadata is merged twice:

```text
PULocationID → Pickup Borough / Pickup Zone

DOLocationID → Drop-off Borough / Drop-off Zone
```

This enables borough-, zone-, and route-level analysis.

---

Pipeline Architecture
```text
Pipeline Architecture
├── Data Ingestion (Parquet)
├── Quality Audit & Anomaly Detection (Meter glitches, non-May dates, negative fares)
├── Data Cleaning & Null Imputation (Boundary filters, unknown location mapping)
├── Spatial Merging (Pickup/Dropoff Boroughs & Zones)
├── Feature Engineering (Temporal metrics, speed, trip categories, payment maps)
└── Downstream Integrations (SQL Data Modeling & Tableau Dashboard)
```
---

# Data Quality Findings

The initial investigation identified several important issues:

* Records outside the May 2026 reporting period
* Pickup timestamps extending into June 2026
* Negative fare amounts
* Negative total amounts
* Extreme trip-distance values
* Extreme fare values
* Zero-distance trips
* Zero passenger counts
* Missing values in several operational fields

For example, the raw data contained a maximum reported trip distance of approximately **307,491 miles**, indicating severe outlier contamination.

The analysis therefore applies explicit cleaning rules before operational metrics are calculated.

---

# Data Cleaning

The primary analytical dataset is restricted to:

```text
May 1, 2026 through May 31, 2026
```

The current cleaning workflow also excludes:

* Non-positive fares
* Fares above the defined analytical threshold
* Non-positive trip distances
* Extreme trip distances

The existing analysis uses:

```python
df_clean = df[
    (df["tpep_pickup_datetime"] >= "2026-05-01")
    & (df["tpep_pickup_datetime"] < "2026-06-01")
    & (df["fare_amount"] > 0)
    & (df["fare_amount"] <= 500)
    & (df["trip_distance"] > 0)
    & (df["trip_distance"] <= 100)
]
```

These thresholds are analytical rules rather than claims about the official validity of every excluded record.

---

# Feature Engineering

The project creates analytical features including:

### Time Features

* `pickup_hour`
* `pickup_day`
* `pickup_month`
* `weekend`
* `day_type`
* `rush_hour`
* `time_of_day`

### Revenue Features

* `tip_pct`
* `revenue_per_mile`

### Operational Features

* `trip_duration_min`
* `speed_mph`
* `trip_category`
* `fare_category`
* `speed_category`

### Geographic Features

* `PU_Borough`
* `PU_Zone`
* `DO_Borough`
* `DO_Zone`
* `borough_route`
* `airport_trip`

---

# Business KPIs

The executive analysis tracks:

* Total Trips
* Total Revenue
* Average Fare
* Average Tip %
* Average Distance
* Average Trip Duration
* Average Speed
* Revenue per Mile

These KPIs form the foundation of the Tableau executive dashboard.

---

# Exploratory Analysis

The Python analysis investigates the following business questions:

### Demand

1. When is taxi demand highest?
2. How does demand vary by day of week?
3. How does weekday demand compare with weekend demand?
4. What are the busiest time-of-day periods?

### Revenue

5. Which hours generate the most revenue?
6. Which boroughs generate the most revenue?
7. Which trip-distance categories generate the most revenue?
8. Which payment methods generate the most revenue?

### Customer Behavior

9. Which payment methods are most commonly used?
10. How does tipping behavior vary by payment method?
11. How does passenger count vary across trips?

### Geography

12. Which pickup zones have the highest demand?
13. Which drop-off zones have the highest demand?
14. Which boroughs have the highest trip volume?
15. Which borough-to-borough routes are most popular?

### Operations

16. How does trip duration vary throughout the day?
17. How does average speed change during rush hour?
18. Which trip categories have the highest revenue per mile?
19. Are airport trips different from non-airport trips?

### Statistical Exploration

20. Which variables are most strongly associated with revenue?
21. How skewed are distance, fare, and revenue distributions?
22. Which metrics contain significant outliers?

---

# SQL Analytics

SQL is used as a complementary analytical layer to demonstrate:

* Aggregation
* CTEs
* Window functions
* Ranking
* Business KPI development
* Route analysis
* Time-series analysis

The SQL analysis is organized into ten analytical areas.

---

# Tableau Dashboard

The final Tableau dashboard provides an executive view of:

* Executive Summary : High-level KPIs, total revenue trend, fare/tip distributions.

* Demand: Hourly demand, Day-of-week demand, Rush-hour demand

* Revenue: Revenue by hour, borough, revenue per mile, payment performance

* Geography: Pickup zones, drop-off zones, borough performance, route performance

* Operations: Trip duration, average speed, airport trips, trip categories

---

# Tools & Technologies

| Tool       | Purpose                                    |
| ---------- | ------------------------------------------ |
| Python     | Cleaning, feature engineering and EDA      |
| Pandas     | Data manipulation                          |
| NumPy      | Numerical analysis                         |
| Matplotlib | Visualization                              |
| Seaborn    | Statistical visualization                  |
| SQL        | Business analytics and aggregation         |
| Tableau    | Executive dashboard                        |
| Git/GitHub | Version control and portfolio presentation |

---

# Project Structure

```text
NYC-Yellow-Taxi-Operations-Intelligence/
├── data/
│   ├── raw/
│   │   ├── yellow_tripdata_2026-05.parquet      # Raw TLC May 2026 Trip Data
│   │   └── taxi_zone_lookup.csv                 # TLC Zone Lookup Metadata
│   └── processed/
│       └── yellow_tripdata_2026-05_cleaned.parquet # Filtered & Feature-Engineered Output
│
├── notebooks/
│   └── NYC_Taxi_Operations_Intelligence_EDA.ipynb # Main Execution Notebook
│
├── sql/
│   └── operational_queries.sql                  # Analytics & KPI SQL
│
├── dashboards/
│   └── NYC_Taxi_Operations_Intelligence.twbx    # Tableau Dashboard Workbook
│
└── README.md
```

---

# Key Business Outcomes

The completed analysis is designed to help stakeholders:

* Improve fleet allocation during high-demand periods
* Identify high-value geographic markets
* Understand airport-related demand
* Evaluate operational efficiency
* Identify revenue opportunities
* Understand customer payment and tipping behavior
* Improve confidence in operational reporting through data-quality controls

---

# Limitations

This analysis covers **one month of Yellow Taxi data**, so conclusions should not automatically be generalized to the entire year.

The dataset also does not independently explain:

* Weather conditions
* Traffic incidents
* Fuel costs
* Driver availability
* Special events
* Taxi supply
* Competitor activity

Therefore, observed relationships should be interpreted as associations rather than causal effects.

---


# Author

**Chinyere Obi**

Data Analyst | Product Analytics | Business Intelligence

Skills demonstrated:

**Python • SQL • Tableau • Data Quality • EDA • Feature Engineering • Business Analytics • Data Storytelling**

