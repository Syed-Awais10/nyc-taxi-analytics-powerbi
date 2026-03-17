# DAX Measures Documentation

All 20+ DAX measures used in the NYC Taxi Analytics dashboards.

## Basic Count & Sum Measures

### Total Trips
```dax
Total Trips = COUNTROWS(Trips)
```
**Purpose:** Count total number of trip records  
**Format:** Whole Number with thousands separator  
**Used in:** All dashboards, primary KPI

### Total Revenue
```dax
Total Revenue = SUM(Trips[total_amount])
```
**Purpose:** Sum of all revenue collected  
**Format:** Currency ($)  
**Used in:** Executive Overview, Pricing Dashboard

### Total Distance
```dax
Total Distance = SUM(Trips[trip_distance])
```
**Purpose:** Total miles traveled across all trips  
**Format:** Decimal (2 places)  
**Used in:** Efficiency calculations

### Total Fare
```dax
Total Fare = SUM(Trips[fare_amount])
```
**Purpose:** Sum of base fares only  
**Format:** Currency ($)  
**Used in:** Revenue breakdown analysis

### Total Tips
```dax
Total Tips = SUM(Trips[tip_amount])
```
**Purpose:** Total tips received  
**Format:** Currency ($)  
**Used in:** Tipping analysis

### Total Passengers
```dax
Total Passengers = SUM(Trips[passenger_count])
```
**Purpose:** Total passenger count  
**Format:** Whole Number  
**Used in:** Capacity analysis

---

## Average Measures

### Avg Fare
```dax
Avg Fare = AVERAGE(Trips[fare_amount])
```
**Purpose:** Average base fare per trip  
**Format:** Currency ($)  
**Used in:** Executive KPI, pricing comparisons

### Avg Trip Distance
```dax
Avg Trip Distance = AVERAGE(Trips[trip_distance])
```
**Purpose:** Average miles per trip  
**Format:** Decimal (2 places)  
**Used in:** Operational efficiency

### Avg Trip Duration
```dax
Avg Trip Duration = AVERAGE(Trips[trip_duration_minutes])
```
**Purpose:** Average trip length in minutes  
**Format:** Decimal (1 place)  
**Used in:** Service quality metrics

### Avg Passengers
```dax
Avg Passengers = AVERAGE(Trips[passenger_count])
```
**Purpose:** Average passengers per trip  
**Format:** Decimal (2 places)  
**Used in:** Capacity planning

### Avg Tip Amount
```dax
Avg Tip Amount = AVERAGE(Trips[tip_amount])
```
**Purpose:** Average tip per trip  
**Format:** Currency ($)  
**Used in:** Tipping behavior analysis

---

## Revenue Optimization Measures

### Revenue Per Mile
```dax
Revenue Per Mile = DIVIDE([Total Revenue], [Total Distance], 0)
```
**Purpose:** Revenue efficiency metric  
**Format:** Currency ($)  
**Used in:** Pricing strategy KPI

### Revenue Per Trip
```dax
Revenue Per Trip = DIVIDE([Total Revenue], [Total Trips], 0)
```
**Purpose:** Average revenue generated per trip  
**Format:** Currency ($)  
**Used in:** Performance benchmarking

### Tip Percentage
```dax
Tip Percentage = 
DIVIDE(
    SUM(Trips[tip_amount]), 
    SUM(Trips[fare_amount]), 
    0
)
```
**Purpose:** Tip as percentage of base fare  
**Format:** Percentage (1-2 decimal places)  
**Used in:** Payment method comparison  
**Key Insight:** Shows 24% for credit vs 0.02% for cash

---

## Operational Efficiency Measures

### Avg Speed MPH
```dax
Avg Speed MPH = 
DIVIDE(
    [Total Distance], 
    SUM(Trips[trip_duration_minutes]) / 60, 
    0
)
```
**Purpose:** Average speed across all trips  
**Format:** Decimal (1 place)  
**Used in:** Traffic and efficiency analysis

---

## Revenue Component Analysis

### Fare Component %
```dax
Fare Component % = 
DIVIDE(
    SUM(Trips[fare_amount]), 
    [Total Revenue], 
    0
) * 100
```
**Purpose:** Base fare as % of total revenue  
**Format:** Percentage  
**Used in:** Revenue breakdown

### Tip Component %
```dax
Tip Component % = 
DIVIDE(
    SUM(Trips[tip_amount]), 
    [Total Revenue], 
    0
) * 100
```
**Purpose:** Tips as % of total revenue  
**Format:** Percentage  
**Used in:** Revenue breakdown

### Extra Component %
```dax
Extra Component % = 
DIVIDE(
    SUM(Trips[extra]), 
    [Total Revenue], 
    0
) * 100
```
**Purpose:** Extra charges as % of total revenue  
**Format:** Percentage  
**Used in:** Surcharge analysis

### Tolls Component %
```dax
Tolls Component % = 
DIVIDE(
    SUM(Trips[tolls_amount]), 
    [Total Revenue], 
    0
) * 100
```
**Purpose:** Tolls as % of total revenue  
**Format:** Percentage  
**Used in:** Route cost analysis

### MTA Tax Component %
```dax
MTA Tax Component % = 
DIVIDE(
    SUM(Trips[mta_tax]), 
    [Total Revenue], 
    0
) * 100
```
**Purpose:** MTA tax as % of total revenue  
**Format:** Percentage  
**Used in:** Tax analysis

---

## Advanced Analytics Measures

### Peak Hour
```dax
Peak Hour = 
VAR MaxTrips = MAXX(VALUES(TimeTable[Hour]), [Total Trips])
RETURN
CALCULATE(
    SELECTEDVALUE(TimeTable[Hour]),
    FILTER(VALUES(TimeTable[Hour]), [Total Trips] = MaxTrips)
)
```
**Purpose:** Identifies hour with highest trip volume  
**Format:** Whole Number (0-23)  
**Used in:** Demand Analysis dashboard  
**Result:** Hour 19 (7 PM)

---

## Supporting Measures

### Total Extra
```dax
Total Extra = SUM(Trips[extra])
```

### Total Tolls
```dax
Total Tolls = SUM(Trips[tolls_amount])
```

### Total Taxes
```dax
Total Taxes = SUM(Trips[mta_tax]) + SUM(Trips[improvement_surcharge])
```

---

## Measure Organization

All measures are stored in the `_Measures` table for organization and easy access.

**Naming Convention:**
- Prefix "Total" for sums
- Prefix "Avg" for averages
- Suffix "%" for percentages
- Use spaces for readability
- Descriptive names (not abbreviated)

**Best Practices Applied:**
- DIVIDE() function used to avoid division by zero errors
- All measures have explicit formatting
- Comments added for complex calculations
- Consistent naming throughout project
