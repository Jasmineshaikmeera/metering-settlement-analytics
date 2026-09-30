# Metering & Settlement Power BI Analytics Dashboard

## Executive Summary
This repository contains an end-to-end **Power BI Analytics Dashboard** designed for an energy utility organization to track meter portfolio growth, customer churn dynamics, energy consumption, and supplier distributions across a 12-month period (**August 2021 – July 2022**).

The underlying data model processes **300,000+ daily settlement records** mapped across 1,000 customer meters.

---

## Key Performance Indicators (Aug 2021 – Jul 2022)

| Metric | Value | Description |
| :--- | :--- | :--- |
| **Total Meters Gained** | **661** | New meter acquisitions during the 12-month period |
| **Total Meters Lost** | **126** | Total customer churn across all loss reasons |
| **Net Portfolio Growth** | **+535** | Net gain in meter inventory (+80.9% acquisition yield) |
| **Total Consumption** | **427.79M kWh** | Total energy settlement volume across active portfolio |
| **Active Meters Inventory**| **845** | Total active meters operating at the end of the period |
| **Average Monthly Consumption**| **35.65M kWh** | Portfolio-wide average monthly energy usage |
| **Data Quality Score** | **100%** | Passed 0 missing dates, 0 duplicates, and 0 unmatched records |

---

## Business Insights & Findings

### 1. Portfolio Growth & Seasonality
* **Peak Acquisition Month:** **October 2021** achieved the highest single-month gain with **82 meters gained** (+75 net gain).
* **Lowest Performance Month:** **March 2022** experienced the highest customer churn with **26 lost meters** (+24 net gain).

### 2. Churn Root Cause Analysis
Total portfolio attrition reached **126 lost meters**. A root-cause breakdown reveals:
* **End of Contract:** **96 meters (76.2%)** — Primary churn driver.
* **Meter Removed:** **20 meters (15.9%)**
* **Terminated:** **10 meters (7.9%)**
> **Strategic Takeaway:** Establishing an automated 60-day proactive contract renewal alert system can mitigate up to 76.2% of customer churn.

### 3. Consumption Patterns (Domestic vs. Industrial)
* **Industrial Meters:** Represent **45.4% of meters (454 units)** but account for **92.84% of total volume (397.17M kWh)**, averaging ~1.02M kWh/month per meter.
* **Domestic Meters:** Account for **54.6% of meters (546 units)** and **7.16% of total volume (30.62M kWh)**, averaging ~66.8K kWh/month per meter.

### 4. Supplier Company Distribution
Analyzed 4 meter equipment suppliers across the 1,000-meter portfolio:
1. **Energy Industries:** 333 meters (33.3%)
2. **Metered ConneXions:** 289 meters (28.9%)
3. **Electric Inc:** 215 meters (21.5%)
4. **Power Meters Ltd:** 163 meters (16.3%)

---

## Technical Architecture & DAX Formulas

### Data Transformation (Power Query)
* Cleaned and transformed daily half-hourly settlement readings into monthly aggregations.
* Validated integrity across foreign keys, confirming **0 duplicate records** and **0 missing gain/loss dates**.

### Core DAX Measures

```dax
// Total Meters Gained
TOTAL METER GAIN = 
CALCULATE(
    COUNT(Meters[Meter_Reference]),
    Meters[Date_Gained] >= DATE(2021,8,1),
    Meters[Date_Gained] <= DATE(2022,7,31)
)
// Total Meters Lost
TOTAL METER LOST = 
CALCULATE(
    DISTINCTCOUNT(Meters[Meter_Reference]),
    USERELATIONSHIP(
        Calendar[Date],
        Meters[Date_Lost]
    ),
    Meters[Date_Lost] >= DATE(2021,8,1),
    Meters[Date_Lost] <= DATE(2022,7,31)
)

// Net Portfolio Growth
NET METERS GAIN/LOST = [TOTAL METER GAIN]-[TOTAL METER LOST]

// Total Energy Consumption
TOTAL CONSUMPTION = SUM(Consumption[DailyHhConsumption])
