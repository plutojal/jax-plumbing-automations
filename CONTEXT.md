# CONTEXT.md — Jax Plumbing & Heating automations

This file provides full project context for Claude Code sessions.
Read this file first before making any changes to the repo.

---

## Project overview

Jax Plumbing & Heating are building a SharePoint-based CRM and operations system. This repo (`jax-plumbing-automations`) stores Power Automate flow definitions.
The Power App frontend lives in a separate repo: https://github.com/plutojal/jax-plumbing-powerapps

---

## SharePoint sites

| Site | URL | Purpose |
|---|---|---|
| Jax Plumbing | `https://jaxplumbinggy.sharepoint.com/sites/JaxPlumbing` | Planner backups, general documents |
| CRM Hub (Live) | `https://jaxplumbinggy.sharepoint.com/sites/JaxPlumbingCRMHub-Live` | CRM lists — Customers, Appliances, Job Addresses, Jobs, Appointments etc. |

All SharePoint connections authenticate as `data@jaxplumbing.co.uk`.

---

---

## SharePoint lists — JaxPlumbing site

### 1. Staff
Master list of all employees. Already live in SharePoint.

**Columns:** Title (full name), Staff Type (Engineer / Subcontractor / Office), Profile (email), Calendar Access Allowed

**Staff titles — exact casing, used for all lookups:**
- Cameron Collings (Engineer)
- Charlotte Cheshire (Office)
- Dan Markwell (Office)
- Danny Coston (Engineer)
- Dave Freeman (Subcontractor)
- Emma Buchan (Office)
- George Buchan (Engineer)
- Harley Rix (Engineer)
- Ian Moore (Subcontractor)
- Jack French (Engineer)
- James Lewis (Subcontractor)
- John Bowen (Engineer)
- Karl Batterbee (Subcontractor)
- Katrina French (Office)
- Kerrie-Ann French (Office)
- Kieran Baldry (Office)
- Kobie Jones (Engineer)
- Lewis Gammon (Engineer)
- Matty Greaves (Engineer)
- Roger Cadd (Engineer)
- Roman Walding (Engineer)
- Ryan Brennand (Engineer)
- Simon Cheshire (Subcontractor)

---

### 2. Qualification Types
Master list of all 48 qualifications. Import file: `Training_Matrix_SharePoint_v4.xlsx` sheet "Qualification Types".

**Columns:** Code, Name, Renewal (Years), Training Provider, Description

**Import first** — Qualification Records depends on this list.

---

### 3. Qualification Records
One row per staff member per qualification. Import file: `Training_Matrix_SharePoint_v4.xlsx` sheet "Qualification Records".

**Columns:**

| Column | Type | Notes |
|---|---|---|
| Title | Text | `Staff Name - Qualification Name` — unique ID |
| Staff | Lookup → Staff | Match on Staff Title |
| Qualification Name | Lookup → Qualification Types | Drives Code, Renewal, Provider |
| Start Date | Date | MM/DD/YYYY |
| Expiry Date | Date | MM/DD/YYYY |
| Certificate Link | Hyperlink | Link to PDF in document library |
| Notes | Text | |

**113 records** across 19 staff members and 48 qualification types.

**SharePoint views to create:**
1. Action required (default) — filter: Expiry Date ≤ Today+30 OR Expiry Date < Today. Sort: Expiry Date asc.
2. Expiring in 60 days — filter: Expiry Date ≤ Today+60 AND Expiry Date > Today-1. Group by: Staff.
3. By employee — no filter, group by Staff (collapsed), sort Staff A–Z then Expiry Date asc.
4. Expired — filter: Expiry Date < Today AND Expiry Date not empty. Sort: Expiry Date asc.
5. All records — no filter, grid style for inline editing.

**Colour formatting:**
- Red row: Expiry Date < Today()
- Amber row: Expiry Date <= Today()+30
- Green: Expiry Date > Today()+30

---

### 4. Vehicles
Company fleet. Import file: `Vehicle_List_SharePoint.xlsx`.

**List name in SharePoint:** Vehicles

**Columns:**

| Column | Type | Notes |
|---|---|---|
| Title | Text | Registration plate — unique ID |
| Registration | Text | |
| Driver | Lookup → Staff | Match on Staff Title |
| Year | Number | |
| Make | Text | |
| Model | Text | |
| Description | Text | |
| Colour | Text | |
| Transmission | Text | Manual / Auto |
| Fuel Type | Text | |
| Vehicle Type | Text | Van / Car / Pick Up |
| TRACKER | Text | Yes / No |
| Next Service Date | Date | |
| Tax Due Date | Date | |
| MOT Due Date | Date | |
| VIN | Text | |

**20 vehicles total.** MV68 WKJ (Yard Van) has no driver — set manually in SharePoint.

---

## SharePoint lists — JaxPlumbingCRMHub-Live site

Base URL: `https://jaxplumbinggy.sharepoint.com/sites/JaxPlumbingCRMHub-Live`

> **All columns on these lists were renamed after creation — their internal names are `field_N`, not display-name-based.** Use the internal field names below for all REST API calls, OData filters, and Power Automate expressions.
>
> **Lookup columns** return a full OData object in GetItems responses. Use `?['ColumnName']?['Value']` for display text and `?['ColumnName']?['Id']` for the numeric ID (e.g. for Get Item calls). Do NOT use `?['ColumnNameId']` — that flat key does not exist.
>
> **MultiChoice fields** (`field_2` Status on Customers and Job Addresses) return `{ results: ["Active"] }` — access with `?['field_2']?['results']`.

---

### 5. Appliances

**List title (REST):** `Appliances` | **URL:** `/Lists/Appliances/AllItems.aspx`
~4,489 items — index any column used in OData filters.

| Display Name | Internal Name | Type | Notes |
|---|---|---|---|
| Title | `Title` | Text | Appliance identifier |
| Appliance Type | `field_3` | Text | |
| Appliance Make | `field_4` | Text | |
| Appliance Model | `field_5` | Text | |
| Appliance Location | `field_6` | Text | |
| Service Due | `field_7` | DateTime | |
| Fuel Type | `field_8` | Choice | Domestic Gas, Commercial Gas, LPG, Oil, ASHP, Unvented Cylinder, Miscellaneous |
| Serial Number | `field_9` | Text | |
| Gas Council Number | `field_10` | Text | |
| Notes | `field_11` | Multi-line | |
| Scheduling Notes | `field_12` | Multi-line | |
| Status | `field_13` | Choice | |
| Customer | `Customer` / `CustomerId` | Lookup → Customers | Use `?['Customer']?['Id']` and `?['Customer']?['Value']` in expressions |
| Job Address | `JobAddress` / `JobAddressId` | Lookup → Job Addresses | Use `?['JobAddress']?['Id']` and `?['JobAddress']?['Value']` in expressions |
| Archive | `Archive` | Boolean | |
| Service Interval Months | `ServiceIntervalMonths` | Choice | |
| ID Val | `IDVal` | Number | |
| Modified | `Modified` | DateTime | System |
| Created | `Created` | DateTime | System |
| Created By | `Author` | User | System |
| Modified By | `Editor` | User | System |

---

### 6. Customers

**List title (REST):** `Customers` | **URL:** `/Lists/Customer/AllItems.aspx`

| Display Name | Internal Name | Type | Notes |
|---|---|---|---|
| Title | `Title` | Text | Unique customer reference |
| Status | `field_2` | MultiChoice | Returns `{ results: [...] }` |
| Full Name | `field_3` | Text | |
| Honorific | `field_4` | Text | Free-text prefix |
| First Name | `field_5` | Text | |
| Last Name | `field_6` | Text | |
| Company Name | `field_7` | Text | |
| Full Address | `field_8` | Text | Concatenated |
| Building/House Number | `field_9` | Text | |
| Street Address | `field_10` | Text | |
| Town/City | `field_11` | Text | |
| Region/County | `field_12` | Text | |
| Postcode | `field_13` | Text | |
| Landline | `field_14` | Text | |
| Mobile | `field_15` | Text | |
| Email | `field_16` | Text | Primary email |
| 2nd Email | `field_17` | Text | Secondary email |
| Notes | `field_18` | Multi-line | |
| Reminders Enabled | `field_19` | Boolean | |
| On Stop | `OnStop` | Boolean | |
| Archive | `Archive` | Boolean | |
| SMS Reminders Enabled | `SMSRemindersEnabled` | Boolean | |
| Email Reminders Enabled | `EmailRemindersEnabled` | Boolean | |
| Honorific Title | `HonorificTitle` | Choice | |
| SMS Marketing Enabled | `SMSMarketingEnabled` | Boolean | |
| Email Marketing Enabled | `EmailMarketingEnabled` | Boolean | |
| SMS Transactional Enabled | `SMSTransactionalEnabled` | Boolean | |
| Email Transactional Enabled | `EmailTransactionalEnabled` | Boolean | |
| SMS Servicing Enabled | `SMSServicingEnabled` | Boolean | |
| Email Servicing Enabled | `EmailServicingEnabled` | Boolean | |
| Modified | `Modified` | DateTime | System |
| Created | `Created` | DateTime | System |
| Created By | `Author` | User | System |
| Modified By | `Editor` | User | System |

---

### 7. Job Addresses

**List title (REST):** `Job Addresses` | **URL:** `/Lists/Job Addresses/AllItems.aspx`

| Display Name | Internal Name | Type | Notes |
|---|---|---|---|
| Title | `Title` | Text | Unique address reference |
| Status | `field_2` | MultiChoice | Returns `{ results: [...] }` |
| Full Address | `field_3` | Text | Concatenated |
| Building/House Number | `field_4` | Text | |
| Street Address | `field_5` | Text | |
| Town/City | `field_6` | Text | |
| Region/County | `field_7` | Text | |
| Postcode | `field_8` | Text | |
| Email | `field_9` | Text | Address-specific email |
| Access Notes | `field_10` | Multi-line | |
| Install Name | `field_11` | Text | Contact name at property |
| Install Phone | `field_12` | Text | Contact phone at property |
| Service Due | `field_13` | DateTime | field_14 is deleted/unused |
| Scheduling Notes | `field_15` | Multi-line | |
| Customer | `Customer` / `CustomerId` | Lookup → Customers | |
| Archive | `Archive` | Boolean | |
| Priority | `Priority` | Choice | |
| ID Val | `IDVal` | Number | |
| SMS Reminders Enabled | `SMSRemindersEnabled` | Boolean | |
| Email Reminders Enabled | `EmailRemindersEnabled` | Boolean | |
| SMS Marketing Enabled | `SMSMarketingEnabled` | Boolean | |
| Email Marketing Enabled | `EmailMarketingEnabled` | Boolean | |
| SMS Transactional Enabled | `SMSTransactionalEnabled` | Boolean | |
| Email Transactional Enabled | `EmailTransactionalEnabled` | Boolean | |
| SMS Servicing Enabled | `SMSServicingEnabled` | Boolean | |
| Email Servicing Enabled | `EmailServicingEnabled` | Boolean | |
| Modified | `Modified` | DateTime | System |
| Created | `Created` | DateTime | System |
| Created By | `Author` | User | System |
| Modified By | `Editor` | User | System |
| Status | Choice | `Status` | |
| Customer | Lookup → Customers | `Customer` / `CustomerId` | ✓ |
| Job Address | Lookup → Job Addresses | `Job_x0020_Address` / `Job_x0020_AddressId` | ✓ |
| Archive | Yes/No | `Archive` | |
| Service Interval Months | Choice | `Service_x0020_Interval_x0020_Months` | |
| ID Val | Number | `ID_x0020_Val` | |

---

## Document library

**Name:** Training Certificates
**Structure:** `Employee Name / Qualification Name / Certificate_Date.pdf`
Each Qualification Record has a Certificate Link column pointing to the relevant PDF.

---

## Power Automate flows

| Folder | Trigger | Description | Status |
|---|---|---|---|
| `mot-monthly-alert` | 1st of month, 08:00 | Email alert for vehicles with MOT due within 30 days | Built |
| `qualification-expiry-alert` | 1st of month, 08:00 | Email alert for qualifications expiring within 30 days | Built |
| `planner-quote-age-sort` | Daily 01:00 UTC | Moves Quotes Planner tasks through age buckets (7→14→28→40→80 days→Cold) | Built |
| `crm-project-quote-age-sort` | Daily 01:00 UTC | Moves CRM Project Quotes Planner tasks through age buckets (14→30→60→90 days→Cold) | Built |
| `planner-weekly-backup` | Saturday 03:00 UTC | Backs up 7 planners to SharePoint CSV (Servicing, Quoted Works, Quotes, Breakdown, General Jobs, Project Install, Project Quotes) | Built |
| `one-off/oil-lpg-contacts` | Manual | Exports all Oil/LPG appliances with customer and job address contact details to CSV | Built — one-off |

---

## Repo conventions

**Flow naming:** `[trigger]-[subject]-[action]` e.g. `mot-monthly-alert`

**Each flow folder contains:**
- `definition.json` — exported from Power Automate (replace placeholder when built)
- `README.md` — trigger, logic, filter query, email details, status checklist

**How to export a flow:**
Power Automate → My Flows → select flow → ••• → Export → Package (.zip) → unzip → copy JSON from `Microsoft.Flow/flows/` → paste into `definition.json`

**How to import a flow:**
Power Automate → My Flows → Import → Import Package (Legacy) → upload ZIP

**Future CI/CD:** `.github/workflows/deploy.yml` is ready to be wired up to the Power Platform CLI (`pac`) for automated deployment on push to `main`. See deploy.yml comments for setup steps.

---

## Key decisions made

- No Power Apps — SharePoint native list views are sufficient for admin-only use
- Staff do not log in to see their own records — admin managed only
- Qualification Types is a separate lookup list (not a choice column) — easier to add new qualifications and stores extra metadata
- Title column on Qualification Records = `Staff Name - Qualification Name` for uniqueness
- Dates in MM/DD/YYYY format throughout (SharePoint default)
- Driver names in Vehicles list match Staff Title exactly for lookup compatibility
- Kerri-Ann French corrected to Kerrie-Ann French to match SharePoint Staff list
- Matthew Greaves normalised to Matty Greaves to match Staff list
