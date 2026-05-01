# qualification-expiry-alert

Sends a monthly email to the admin listing all staff qualifications expiring within the next 30 days.

## Trigger

Scheduled — runs on the 1st of every month at 08:00.

## Logic

1. Get all items from the **Qualification Records** SharePoint list
2. Filter where `Expiry Date` is less than or equal to today + 30 days
3. Filter where `Expiry Date` is not empty
4. Sort by `Expiry Date` ascending, then by `Staff` A–Z
5. Group results by Staff name for a readable email
6. If any results — send a summary email via Outlook
7. If no results — no email sent

## Filter query (OData)

```
ExpiryDate le '[Today+30]' and ExpiryDate ne null
```

## Email

**To:** admin@jaxplumbing.co.uk  
**Subject:** `Qualification Alert — [X] qualification(s) expiring within 30 days`  
**Body:** Table grouped by staff member listing: Staff, Qualification Name, Expiry Date

## SharePoint list

**List:** Qualification Records  
**Site:** [your SharePoint site URL]  
**Columns used:** Title, Staff, Qualification Name, Expiry Date, Certificate Link

## Related lists

- **Staff** — lookup source for the Staff column
- **Qualification Types** — lookup source for Qualification Name (also provides Code, Renewal, Provider)

## Status

- [ ] Built in Power Automate
- [ ] Tested
- [ ] definition.json exported and committed

## Change log

| Date | Change | Author |
|---|---|---|
| | Initial setup | |
