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
MOTDueDate le '@{formatDateTime(addDays(utcNow(), 30), 'yyyy-MM-dd')}' and MOTDueDate ne null
```

**Order by:** `MOTDueDate asc`

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
- [x] definition.json template committed — replace with exported JSON after building in Power Automate

## Setup notes

1. Open `definition.json` and replace `https://[tenant].sharepoint.com/sites/JaxPlumbing` with your actual SharePoint site URL.
2. Import the flow via **Power Automate → My Flows → Import → Import Package (Legacy)**.
3. When prompted, link the two connections: **SharePoint** and **Office 365 Outlook**.
4. Run a test manually from the flow editor and verify the email arrives.
5. Export the live flow and replace `definition.json` with the exported JSON.

## Change log

| Date | Change | Author |
|---|---|---|
| | Initial setup | |
