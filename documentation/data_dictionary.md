# Data Dictionary

Complete data schema for NYC Taxi Analytics project.

## Trips Table (Fact Table)

Main transaction table containing 12,420,346 trip records.

| Column Name | Data Type | Description | Example | Business Use |
|-------------|-----------|-------------|---------|--------------|
| VendorID | Integer | Taxi vendor identifier (1 or 2) | 1 | Vendor performance comparison |
| tpep_pickup_datetime | DateTime | Trip start date and time | 2015-01-15 19:05:39 | Temporal analysis |
| tpep_dropoff_datetime | DateTime | Trip end date and time | 2015-01-15 19:23:12 | Duration calculation |
| passenger_count | Integer | Number of passengers (1-6) | 2 | Capacity utilization |
| trip_distance | Decimal | Distance traveled in miles | 3.45 | Fare correlation |
| pickup_longitude | Decimal | Pickup location longitude | -73.9821 | Geographic analysis |
| pickup_latitude | Decimal | Pickup location latitude | 40.7614 | Geographic analysis |
| RateCodeID | Integer | Rate code (1=Standard, 2=JFK, etc.) | 1 | Pricing tier analysis |
| store_and_fwd_flag | String | Data storage flag (Y/N) | N | Connectivity monitoring |
| dropoff_longitude | Decimal | Drop-off longitude | -73.9645 | Route analysis |
| dropoff_latitude | Decimal | Drop-off latitude | 40.7654 | Route analysis |
| payment_type | Integer | Payment method (1=Credit, 2=Cash) | 1 | Tip analysis |
| fare_amount | Decimal | Base fare in dollars | $11.50 | Revenue analysis |
| extra | Decimal | Extra charges | $0.50 | Revenue breakdown |
| mta_tax | Decimal | MTA tax ($0.50) | $0.50 | Tax analysis |
| tip_amount | Decimal | Tip (credit card only) | $2.00 | Tipping behavior |
| tolls_amount | Decimal | Tolls paid | $5.76 | Route cost analysis |
| improvement_surcharge | Decimal | Surcharge ($0.30) | $0.30 | Revenue breakdown |
| total_amount | Decimal | Total charge | $20.56 | Total revenue |

## Dimension Tables

### DateTable
Calendar dimension for temporal analysis.

| Column | Type | Description |
|--------|------|-------------|
| Date | Date | Calendar date (PK) |
| Year | Integer | Year (2015) |
| Month | String | Month name |
| MonthNum | Integer | Month number (1-12) |
| Day | Integer | Day of month |
| DayOfWeek | String | Day name (Monday-Sunday) |
| DayOfWeekNum | Integer | Day number (1-7) |
| Quarter | String | Quarter (Q1-Q4) |
| IsWeekend | String | Weekend/Weekday flag |

### TimeTable
Hourly dimension for time-of-day analysis.

| Column | Type | Description |
|--------|------|-------------|
| Hour | Integer | Hour (0-23) (PK) |
| TimeOfDay | String | Period (Morning/Afternoon/Evening/Night) |

### RateCodeDim
Rate code descriptions.

| RateCodeID | RateCodeName |
|------------|--------------|
| 1 | Standard Rate |
| 2 | JFK Airport |
| 3 | Newark Airport |
| 4 | Nassau/Westchester |
| 5 | Negotiated Fare |
| 6 | Group Ride |
| 99 | Unknown/Other |

### PaymentTypeDim
Payment method descriptions.

| payment_type | PaymentTypeName |
|--------------|-----------------|
| 1 | Credit Card |
| 2 | Cash |
| 3 | No Charge |
| 4 | Dispute |
| 5 | Unknown |
| 6 | Voided Trip |

## Calculated Columns

### trip_duration_minutes
```
Duration in minutes = dropoff_datetime - pickup_datetime
```

### pickup_hour
```
Hour extracted from pickup_datetime (0-23)
```

### day_of_week
```
Day name from pickup_datetime
```

### is_weekend
```
"Weekend" if Saturday/Sunday, else "Weekday"
```

### time_of_day
```
Morning: 6-12
Afternoon: 12-17
Evening: 17-21
Night: 21-6
```

### fare_bins
```
Categorical grouping of fare amounts into ranges:
$0-$10, $10-$20, $20-$30, $30-$40, $40-$50, $50-$75, $75+
```

### pickup_zone
```
Geographic zone based on lat/long coordinates:
- Midtown Manhattan
- Downtown Manhattan
- Upper Manhattan
- JFK Area
- Other Areas
```

## Data Quality Notes

**Cleaning steps applied:**
- Removed trips with null pickup/dropoff times
- Filtered fare_amount > $0
- Filtered trip_distance > 0 and <= 100 miles
- Validated coordinates within NYC boundaries
- Removed passenger_count outside 1-6 range
- Filtered fare outliers (> $500)

**Data coverage:** January 2015 (full month)
**Total records after cleaning:** 12,420,346 trips
