# CWD Tenant Metering SkySpark Process

## 1. Data Collection
Data is collected from the building's Niagara system using the Arcbeam connector to IMEG's AWS instance of SkySpark.

1. Points to be collect data were determined from the documentation provided by CWD Group to IMEG [LINK].
2. These points reside on a piece of equipment OR a tenant meter (e.g Cooling Tower 1, or Starbucks BTU Meter)
3. Each point is configured to collect data every 15minutes
4. In the event that a point has a history/trend enabled, those are pulled in as backups -- ADD FREQUENCY

## 2. Data Aggregation
Each Tenant Meter is its own point/equipment (e.g. Starbucks Heating Energy) that collects data. To understand which meter goes to which usage, the equipment have been aggregated into equipGroupingRefs (e.g. "Commercial").

These equipGroupingRefs, or Tenant Types, is aggregated into a Total Meter (e.g. Commercial Cooling Energy). These points are computed points running ``` hisFunc_cwd_coolingEnergy ```, which aggregates all tenant meters underneath a Tenant Type, or Total Meter.

# CWD Tenant Metering Monthly Process

## Helpful Links:
  - [SkySpark Tasks](http://34.212.16.148/ui/cwdGroup_cloud?view=taskDebug#623530b3700d)
  - [SkySpark Billing Dashboard](http://34.212.16.148/ui/cwdGroup_cloud?view=usageValidation#af5e30b3743f)

## 1. Monthly Usages & Allocations for IMEG Review
This portion of the process aggregates monthly bills for each entity into a single table for simple review. It sends out on the first of each month with data for the previous month to Laura Barber and Tanner Cooper. They are declared as email recipients in the task expression.

**Date:** 1st of each month
**Recipients:** Tanner Cooper, Laura Barber

1. Data is aggregated from the following equations:
   
    - ``` ecapHelper_MonthlyImport ```
    - ``` view_tenantAllocationPercentages ```
  
3. The above functions produce ``` report_monthlyUsagesForReview ```
4. This is done monthly on the first of each month for "lastMonth()"s data
5. Once Tanner/Laura receive the usages, they email each other indicating approval and investigate any issues.

## 2. Monthly Usages & Allocations for Darrin
This task will run the same function as Item 1, but now include Darrin from CWD Group as a recipient. To run it, a SkySpark user needs to manually hit "Send."

## 3. Monthly ECAP Bill Data Push
This represents the monthly bill data for each Entity (Commercial Water for example). This generates the bill in EnergyCAP that the entities will receive. It will only run once IMEG and Darrin have approved of the totals first.

1. Data from meters is aggregated into Entity Meters (Commercial, Residential, Hotel, Whole Foods).
2. Data from Entity Meters is totaled up for the month in these functions:
   
   - **Cooling** -- ```view_meterValidation_cooling_table_totalMeters```
   - **Heating** -- ```view_meterValidation_heating_table_totalMeters```
   - **Water** -- ```view_meterValidation_water_table_totalMeters```

4.The data from 2 is called in the ecapHelper_MonthlyImport func. This produces a table of usage, cost and plant allocation for each entity for each month called.
   - For example, Residential Water outputs this table
6. This data is called by ecapMonthlyImportPOST. This function takes the cost/usage data and puts it into a format that EnergyCAP can understand. This function can be run independently on a point.
7. The data from ecapMonthlyImportPOST is then called each month in the ecapMonthlyImportPOSTAll function. This is set up to run as a task and just runs all of these tasks in a single loop.

## ECAP – Bills to Client
This is the final step in the process. Once all data has been put into ECAP, it will generate bills and send them to the correct people.

1. For each person receiving a bill, they need an ECAP user
2. Make sure their ECAP user only has access to the entity that they need to see (e.g Commercial).
3. Add that user to a distribution list for a certain bill
4. Set up the bills to send on the 15th of each month to send the data for the previous month
5. Make sure the bills will NOT send if data is not present.

**Example function call:**
```
report_monthlyUsagesForReview(read(site),lastMonth(),["tanner.r.cooper@imegcorp.com"])
```
