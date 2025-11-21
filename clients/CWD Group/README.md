# CWD Data Processes

## 1. Monthly Usages & Allocations for IMEG Review
This function aggregates monthly bills for each entity into a single table. It sends out on the first of each month with data for the previous month to Laura Barber and Tanner Cooper. They are declared as email recipients in the task expression.

**Date:** 1st of each month
**Recipients:** Tanner Cooper, Laura Barber

1. Data is aggregated from ecapHelper_MonthlyImport & view_tenantAllocationPercentages funcs into the report_monthlyUsagesForReview func.
2. This is done monthly on the first of each month for "lastMonth()"s data
3. Once Tanner/Laura receive the usages, they email each other indicating approval and investigate any issues.

## 2. Monthly Usages & Allocations for Darrin
This task will run the same function as Item 1, but now include Darrin from CWD Group as a recipient.

## 3. Monthly ECAP Bill Data Push
This represents the monthly bill data for each Entity (Commercial Water for example). This generates the bill in EnergyCAP that the entities will receive. It will only run once IMEG and Darrin have approved of the totals first.

1. Data from meters is aggregated into Entity Meters (Commercial, Residential, Hotel, Whole Foods).
2. Data from Entity Meters is totaled up for the month in these functions:
   - **Cooling** -- view_meterValidation_cooling_table_totalMeters
   - **Heating** -- view_meterValidation_heating_table_totalMeters
   - **Water** -- view_meterValidation_water_table_totalMeters
3. The data from 2 is called in the ecapHelper_MonthlyImport func. This produces a table of usage, cost and plant allocation for each entity for each month called.
   - For example, Residential Water outputs this table
4. This data is called by ecapMonthlyImportPOST. This function takes the cost/usage data and puts it into a format that EnergyCAP can understand. This function can be run independently on a point.
5. The data from ecapMonthlyImportPOST is then called each month in the ecapMonthlyImportPOSTAll function. This is set up to run as a task and just runs all of these tasks in a single loop.

## ECAP – Bills to Client
This is the final step in the process. Once all data has been put into ECAP, it will generate bills and send them to the correct people.

1. For each person receiving a bill, they need an ECAP user
2. Make sure their ECAP user only has access to the entity that they need to see (e.g Commercial).
3. Add that user to a distribution list for a certain bill
4. Set up the bills to send on the 15th of each month to send the data for the previous month
5. Make sure the bills will NOT send if data is not present.

---

**Example function call:**
```
report_monthlyUsagesForReview(read(site),lastMonth(),["tanner.r.cooper@imegcorp.com"])
```
