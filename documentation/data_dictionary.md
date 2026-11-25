
# Data Dictionary
 
Schema of the NYC Taxi Analytics Power BI model.
 
**Source:** NYC Taxi & Limousine Commission (TLC) yellow taxi trip records, January 2015 (Kaggle copy, 1.84 GB CSV, 19 columns). Descriptions of the raw columns follow the TLC data dictionary.
 
## Model overview
 
| Table | Role | Rows | How it is built |
|---|---|---|---|
| `Trips` | Fact | 12,420,345 | CSV loaded and cleaned in Power Query, plus two DAX columns |
| `DateTable` | Dimension | 31 | DAX calculated table (`CALENDAR`, Jan 1-31, 2015) |
| `TimeTable` | Dimension | 24 | DAX calculated table (`GENERATESERIES`, hours 0-23) |
| `RateCodeDim` | Dimension | 7 | DAX calculated table (`DATATABLE`) |
| `PaymentTypeDim` | Dimension | 6 | DAX calculated table (`DATATABLE`) |
| `_Measures` | Measures only | - | 36 measures (see `dax_measures.md`) |
 
### Relationships
 
All four are active, many-to-one, and filter from the dimension to `Trips`.
 
| From (`Trips`) | To | Cardinality |
|---|---|---|
| `pickup_date` | `DateTable[Date]` | many-to-one |
| `pickup_hour` | `TimeTable[Hour]` | many-to-one |
| `RateCodeID` | `RateCodeDim[RateCodeID]` | many-to-one |
| `payment_type` | `PaymentTypeDim[payment_type]` | many-to-one |
 
## Trips table (fact)
 
`Trips` has 25 columns: 19 loaded from the CSV, 4 added in Power Query and 2 added in DAX.
 
### Columns loaded from the CSV
 
| Column | Type | Description | Notes |
|---|---|---|---|
| `VendorID` | Whole number | TLC provider: 1 = Creative Mobile Technologies, 2 = VeriFone | |
| `tpep_pickup_datetime` | Date/time | Date and time the meter was engaged | Cleaning: must not be empty |
| `tpep_dropoff_datetime` | Date/time | Date and time the meter was disengaged | Cleaning: must not be empty |
| `passenger_count` | Whole number | Number of passengers (driver-entered) | Cleaning: kept 1 to 6 |
| `trip_distance` | Decimal | Trip distance in miles reported by the taximeter | Cleaning: kept > 0 and <= 100 |
| `pickup_longitude` | Decimal | Pickup longitude | Cleaning: kept -74.3 to -73.7 |
| `pickup_latitude` | Decimal | Pickup latitude | Cleaning: kept 40.5 to 40.9 |
| `RateCodeID` | Whole number | Final rate code: 1 Standard, 2 JFK, 3 Newark, 4 Nassau/Westchester, 5 Negotiated, 6 Group ride; 99 appears in the data and is labelled Unknown/Other | |
| `store_and_fwd_flag` | Text | Y if the trip record was held in the vehicle before being sent to the vendor, N otherwise | |
| `dropoff_longitude` | Decimal | Dropoff longitude | Cleaning: kept -74.3 to -73.7 |
| `dropoff_latitude` | Decimal | Dropoff latitude | Not filtered |
| `payment_type` | Whole number | 1 Credit card, 2 Cash, 3 No charge, 4 Dispute, 5 Unknown, 6 Voided trip | |
| `fare_amount` | Decimal | Time-and-distance fare calculated by the meter | Cleaning: kept > 0. Not capped (maximum is $900; only 54 trips exceed $200) |
| `extra` | Decimal | Miscellaneous extras and surcharges (the $0.50 and $1 rush hour and overnight charges) | |
| `mta_tax` | Decimal | $0.50 MTA tax triggered by the metered rate | |
| `tip_amount` | Decimal | Tip amount. **Populated automatically for credit card tips only; cash tips are not recorded** | Cleaning: kept <= $1,000 (removes one corrupt row with a $3,950,588.80 tip) |
| `tolls_amount` | Decimal | Total tolls paid | |
| `improvement_surcharge` | Decimal | $0.30 improvement surcharge assessed at the flag drop (levied from 2015) | |
| `total_amount` | Decimal | Total amount charged to passengers; does not include cash tips | |
 
### Columns added in Power Query
 
| Column | Type | Definition |
|---|---|---|
| `trip_duration_minutes` | Decimal | Dropoff time minus pickup time, in exact (fractional) minutes |
| `pickup_hour` | Whole number | Hour of the pickup time, 0-23 |
| `pickup_date` | Date | Date of the pickup time |
| `time_of_day` | Text | Morning (hours 6-11), Afternoon (12-16), Evening (17-20), Night (21-5) |
 
### Columns added in DAX
 
**`fare_bins`** (text) groups `fare_amount` into `$0-$10`, `$10-$20`, `$20-$30`, `$30-$40`, `$40-$50`, `$50-$75` and `$75+`. Each upper bound is inclusive (a $10.00 fare falls in `$0-$10`).
 
**`pickup_zone`** (text) assigns each pickup to an approximate latitude/longitude box. The boxes are checked in this order and the first match wins:
 
| Zone | Latitude | Longitude | Share of trips |
|---|---|---|---|
| Central Manhattan (34th-110th St) | 40.75 to 40.80 | -74.00 to -73.95 | 49.9% |
| Lower Manhattan (below 34th St) | 40.70 to below 40.75 | -74.02 to -73.97 | 38.7% |
| JFK Area | 40.63 to 40.68 | -73.80 to -73.75 | 1.8% |
| LaGuardia Area | 40.76 to 40.79 | -73.90 to -73.85 | 2.0% |
| Other Areas | everything else (outer boroughs, Harlem and above, and so on) | | 7.7% |
 
These are hand-drawn bounding boxes, not official TLC taxi zones, and the street numbers in the zone names are approximate.
 
## Dimension tables
 
### DateTable
 
One row per day, Jan 1-31, 2015.
 
| Column | Type | Description |
|---|---|---|
| `Date` | Date | Calendar date (joins to `Trips[pickup_date]`) |
| `Year` | Whole number | 2015 |
| `Month` | Text | Month name |
| `MonthNum` | Whole number | 1 |
| `Day` | Whole number | Day of month, 1-31 |
| `DayOfWeek` | Text | Full day name (Monday to Sunday) |
| `DayOfWeekNum` | Whole number | 1 = Sunday through 7 = Saturday (use it to sort `DayOfWeek`) |
| `Quarter` | Text | `Q1` |
| `IsWeekend` | Text | `Weekend` for Saturday and Sunday, otherwise `Weekday` |
 
January 2015 has five Thursdays, Fridays and Saturdays and four of every other weekday, so day comparisons use average trips per day.
 
### TimeTable
 
One row per hour of the day.
 
| Column | Type | Description |
|---|---|---|
| `Hour` | Whole number | 0-23 (joins to `Trips[pickup_hour]`) |
| `TimeOfDay` | Text | Morning 6-11, Afternoon 12-16, Evening 17-20, Night 21-5 |
| `Value` | Whole number | Leftover column from `GENERATESERIES`; duplicates `Hour` and can be hidden |
 
### RateCodeDim
 
| RateCodeID | RateCodeName |
|---|---|
| 1 | Standard Rate |
| 2 | JFK Airport |
| 3 | Newark Airport |
| 4 | Nassau/Westchester |
| 5 | Negotiated Fare |
| 6 | Group Ride |
| 99 | Unknown/Other |
 
### PaymentTypeDim
 
| payment_type | PaymentTypeName |
|---|---|
| 1 | Credit Card |
| 2 | Cash |
| 3 | No Charge |
| 4 | Dispute |
| 5 | Unknown |
| 6 | Voided Trip |
 
## Data quality notes
 
**Row counts:** 12,748,986 raw rows; 12,420,345 after cleaning (328,641 removed, 2.58%).
 
**Cleaning steps (Power Query, in order):**
 
1. Pickup datetime is not empty
2. Dropoff datetime is not empty
3. `fare_amount > 0`
4. `trip_distance > 0`
5. `trip_distance <= 100`
6. `passenger_count` between 1 and 6
7. Pickup longitude between -74.3 and -73.7
8. Pickup latitude between 40.5 and 40.9
9. Dropoff longitude between -74.3 and -73.7
10. `tip_amount <= 1000`
**Not applied:** no fare cap, and no filter on dropoff latitude.
 
**Known limitations:**
- Cash tips are not recorded in the source, so cash trips show 0% tips. Tip analysis uses credit card trips only.
- The data covers January 2015 only. January 1 and the Jan 26-27 snowstorm distort some days.
- Zones are approximate bounding boxes rather than official taxi zones.
 
