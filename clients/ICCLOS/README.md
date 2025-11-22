# ICCLOS DATA COLLECTION PROCESS

## Momentus
1. Data from Momentus is brought in on a daily basis using ```task_createMomentusRecs```

   - Events --> ```view_momentusEventsV2(conn,thisWeek(),10000)```, ```action_createMomentusRecs_V2(events)```
   - Spaces --> ```view_momentusSpacesV2(conn)```, ```action_createMomentusRecs_V2(spaces)```
   - Bookings --> ```view_momentusBookingsV2(conn,e->eventID,thisWeek(),10000)```, ```action_createMomentusRecs_V2(bookings)```

2. That data is stored as one of 3 SkySpark recs (event, bookings, spaces)

## Submeters
Data from submeters is collected via BACnet from the meters themselves, or from Metasys. We are collecting data for the following utilities:

- Electricity
- Chilled Water
- Steam
- Gas (not yet at ICC)

# ICCLOS EVENT USAGE CALCULATION PROCESS

## Event Calculations for IMEG to Review
This process aggregates the events at ICCLOS for the past month and their utility usage and sends to IMEG for a sanity check.

- Date: 1st of each month
- Recipients: Tanner Cooper, Laura Barber

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

## Monthly Usages & Allocations for Tom to Review
This task will run the same function as Process 1, but now include Tom Boyle as a recipient to approve.
