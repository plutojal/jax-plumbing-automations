# mot-monthly-alert

Sends a monthly email to the admin listing all vehicles with an MOT due within the next 30 days.

## Trigger

Scheduled — runs on the 1st of every month at 08:00.

## Logic

1. Get all items from the **Vehicles** SharePoint list
2. Filter where `MOT Due Date` is less than or equal to today + 30 days
3. Filter where `MOT Due Date` is not empty
4. Sort by `MOT Due Date` ascending
5. If any results — send a summary email via Outlook
6. If no results — no email sent

## Filter query (OData)

```
MOTDueDate le '[Today+30]' and MOTDueDate ne null
```

## Email

**To:** admin@jaxplumbing.co.uk  
**Subject:** `MOT Alert — [X] vehicle(s) due within 30 days`  
**Body:** Table listing: Registration, Driver, Make, Model, MOT Due Date

## SharePoint list

**List:** Vehicles  
**Site:** [your SharePoint site URL]  
**Columns used:** Title, Registration, Driver, Make, Model, MOT Due Date

## Status

- [ ] Built in Power Automate
- [ ] Tested
- [ ] definition.json exported and committed

## Change log

| Date | Change | Author |
|---|---|---|
| | Initial setup | |
