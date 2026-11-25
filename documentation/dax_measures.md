
# DAX Measures
 
The `_Measures` table holds **36 measures**: 24 core KPIs, 5 analysis measures and 7 data-audit measures. Formulas below are exported from the model with Power BI's DAX query view. Many are simple aggregations; the more involved ones are noted.
 
All ratio measures use `DIVIDE()` so a zero denominator returns the alternate result instead of an error.
 
## Core measures (24)
 
### Totals
 
```dax
Total Trips      = COUNTROWS(Trips)
Total Revenue    = SUM(Trips[total_amount])
Total Distance   = SUM(Trips[trip_distance])
Total Fare       = SUM(Trips[fare_amount])
Total Tips       = SUM(Trips[tip_amount])
Total Passengers = SUM(Trips[passenger_count])
Total Extra      = SUM(Trips[extra])
Total Tolls      = SUM(Trips[tolls_amount])
Total Taxes      = SUM(Trips[mta_tax]) + SUM(Trips[improvement_surcharge])
```
 
`Total Revenue` sums `total_amount` (fare, extras, taxes, tolls and card tips).
 
### Averages
 
```dax
Avg Fare           = AVERAGE(Trips[fare_amount])
Avg Trip Distance  = AVERAGE(Trips[trip_distance])
Avg Trip Duration  = AVERAGE(Trips[trip_duration_minutes])
Avg Passengers     = AVERAGE(Trips[passenger_count])
Avg Tip Amount     = AVERAGE(Trips[tip_amount])
```
 
`Avg Tip Amount` averages over every trip in the filter context, so cash trips (no recorded tip) pull it down unless a credit card filter is applied.
 
### Efficiency
 
```dax
Revenue Per Mile  = DIVIDE([Total Revenue], [Total Distance], 0)
Revenue Per Trip  = DIVIDE([Total Revenue], [Total Trips], 0)
Avg Speed MPH     = DIVIDE([Total Distance], SUM(Trips[trip_duration_minutes]) / 60, 0)
```
 
`Avg Speed MPH` is total miles divided by total hours, so it weights longer trips more heavily than an average of per-trip speeds.
 
### Tipping
 
```dax
Tip Percentage = DIVIDE(SUM(Trips[tip_amount]), SUM(Trips[fare_amount]), 0)
```
 
Tip as a share of base fare (`fare_amount` only, not the total bill). Cash tips are not recorded in the source data, so this measure is only meaningful for credit card trips; see `Card Tip %` below.
 
### Revenue components
 
```dax
Fare Component %    = DIVIDE(SUM(Trips[fare_amount]), [Total Revenue], 0) * 100
Tip Component %     = DIVIDE(SUM(Trips[tip_amount]), [Total Revenue], 0) * 100
Extra Component %   = DIVIDE(SUM(Trips[extra]), [Total Revenue], 0) * 100
Tolls Component %   = DIVIDE(SUM(Trips[tolls_amount]), [Total Revenue], 0) * 100
MTA Tax Component % = DIVIDE(SUM(Trips[mta_tax]), [Total Revenue], 0) * 100
```
 
These return values already multiplied by 100 (for example 12.5 rather than 0.125), so format them as numbers, not as percentages.
 
### Peak hour
 
```dax
Peak Hour =
VAR MaxTrips = MAXX(VALUES(TimeTable[Hour]), [Total Trips])
RETURN
CALCULATE(
    SELECTEDVALUE(TimeTable[Hour]),
    FILTER(VALUES(TimeTable[Hour]), [Total Trips] = MaxTrips)
)
```
 
Returns the hour (0-23) with the most trips in the current filter context, and blank if two hours tie. Across the whole month it returns 19 (7 PM).
 
## Analysis measures (5)
 
```dax
Avg Trips Per Day = DIVIDE([Total Trips], DISTINCTCOUNT(DateTable[Date]))
 
Trips % of Total  = DIVIDE([Total Trips], CALCULATE([Total Trips], ALLSELECTED()))
Fare % of Total   = DIVIDE([Total Fare], CALCULATE([Total Fare], ALLSELECTED()))
 
Card Tip % = CALCULATE([Tip Percentage], PaymentTypeDim[PaymentTypeName] = "Credit Card")
 
Share Fare Up To 20 =
DIVIDE(CALCULATE([Total Trips], Trips[fare_amount] <= 20), [Total Trips])
```
 
- `Avg Trips Per Day` divides by the number of distinct dates in the current filter context. January 2015 has five Thursdays, Fridays and Saturdays and four of every other weekday, so this is the fair way to compare days.
- `Trips % of Total` and `Fare % of Total` use `ALLSELECTED()`, so shares are calculated within whatever the slicers have selected.
- `Card Tip %` restricts `Tip Percentage` to credit card trips.
## Data-audit measures (7)
 
Used to find the corrupt tip record during validation.
 
```dax
Max Fare            = MAX(Trips[fare_amount])
Max Tip             = MAX(Trips[tip_amount])
Trips Fare Over 200 = CALCULATE([Total Trips], Trips[fare_amount] > 200)
Trips Tip Over 100  = CALCULATE([Total Trips], Trips[tip_amount] > 100)
 
Tip % (tip ≤ 100) =
DIVIDE(
    CALCULATE(SUM(Trips[tip_amount]), Trips[tip_amount] <= 100),
    CALCULATE(SUM(Trips[fare_amount]), Trips[tip_amount] <= 100)
)
 
Trips Tip Over 1000 = CALCULATE([Total Trips], Trips[tip_amount] > 1000)
Tips Over 1000 ($)  = CALCULATE(SUM(Trips[tip_amount]), Trips[tip_amount] > 1000)
```
 
Before the $1,000 tip cap was added in Power Query, `Trips Tip Over 1000` returned 1 and `Tips Over 1000 ($)` returned $3,950,588.80. That single row had inflated the card tip rate from 20.0% to 24.15%. `Trips Fare Over 200` returned 54 and `Trips Tip Over 100` returned 64 (both measured before the cap), and the largest fare is $900.
 
## Reference values
 
Values from the current model (12,420,345 trips):
 
| Measure | Value |
|---|---|
| Total Trips | 12,420,345 |
| Total Revenue | $182.9M |
| Avg Fare | $11.84 |
| Avg Trip Distance | 2.80 miles |
| Revenue Per Mile | $5.27 |
| Peak Hour | 19 |
| Card Tip % (credit card trips) | 20.0% |
| Avg Tip Amount (credit card trips) | $2.48 |
| Tip Percentage (all trips, cash unrecorded) | 12.97% |
| Share Fare Up To 20 | 0.89 |
| Max Tip (after the cap) | $888.20 |
 
## Organization
 
All measures live in the `_Measures` table. Names use the prefixes `Total` for sums and `Avg` for averages, and a `%` suffix for percentages.
 
