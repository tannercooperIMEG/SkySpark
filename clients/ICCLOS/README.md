# ICCLOS DATA COLLECTION PROCESS

## Momentus

## Submeters

# ICCLOS EVENT USAGE CALCULATION PROCESS

1. Event Calculations for IMEG to Review
This function aggregates the events at ICCLOS for the past month and their utility usage and sends to IMEG for a sanity check.

Date: 1st of each month
Recipients: Tanner Cooper, Laura Barber

1.	Event data is pulled from view_eventTracking_events into report_eventUsageSummary
2.	The utility usage for each event is calculated using the below functions:
a.	Elec -- view_eventTracking_elecMeters
b.	CHW - view_eventTracking_chwMeters
c.	Steam – view_eventTracking_steam
d.	Gas -- view_eventTracking_gas
3.	The demand for each event is pulled from testDemandCalc
4.	The events are aggregated into a table of cost in report_eventUsageSummary that is emailed to Tanner/Laura for review
a.	A demand table is also included to help verify values.
b.	In the email is included total calculated utility cost and how that compares to past utility usages for ICCLOS
5.	Laura/Tanner indicates approval and we move to task 2

2. Monthly Usages & Allocations for Tom to Review
This task will run the same function as Item 1, but now include Tom Boyle as a recipient to approve.
