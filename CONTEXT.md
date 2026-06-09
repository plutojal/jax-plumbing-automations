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

> Internal column names follow the SharePoint URL-encoding convention: spaces → `_x0020_`, slash `/` → `_x002F_`. Single-word column names keep their display name as the internal name. Where a column has been verified working in a flow expression, it is marked ✓.

### 5. Customers

**List URL name:** `Customer` (display name is "Customers")

| Display Name | Type | Internal Name | Notes |
|---|---|---|---|
| Title | Text | `Title` | Customer reference |
| Status | Choice | `Status` | |
| Full Name | Text | `Full_x0020_Name` | |
| Honorific | Text | `Honorific` | |
| Honorific Title | Choice | `Honorific_x0020_Title` | |
| First Name | Text | `First_x0020_Name` | |
| Last Name | Text | `Last_x0020_Name` | |
| Company Name | Text | `Company_x0020_Name` | |
| Full Address | Text | `Full_x0020_Address` | |
| Building/House Number | Text | `Building_x002F_House_x0020_Number` | |
| Street Address | Text | `Street_x0020_Address` | |
| Town/City | Text | `Town_x002F_City` | |
| Region/County | Text | `Region_x002F_County` | |
| Postcode | Text | `Postcode` | |
| Landline | Text | `Landline` | ✓ used in oil-lpg-contacts |
| Mobile | Text | `Mobile` | ✓ used in oil-lpg-contacts |
| Email | Text | `Email` | ✓ used in oil-lpg-contacts |
| 2nd Email | Text | `_x0032_nd_x0020_Email` | Leading digit encoded |
| Notes | Multi-line | `Notes` | |
| Reminders Enabled | Yes/No | `Reminders_x0020_Enabled` | |
| On Stop | Yes/No | `On_x0020_Stop` | |
| Archive | Yes/No | `Archive` | |
| SMS Reminders Enabled | Yes/No | `SMS_x0020_Reminders_x0020_Enabled` | |
| Email Reminders Enabled | Yes/No | `Email_x0020_Reminders_x0020_Enabled` | |
| SMS Marketing Enabled | Yes/No | `SMS_x0020_Marketing_x0020_Enabled` | |
| Email Marketing Enabled | Yes/No | `Email_x0020_Marketing_x0020_Enabled` | |
| SMS Transactional Enabled | Yes/No | `SMS_x0020_Transactional_x0020_Enabled` | |
| Email Transactional Enabled | Yes/No | `Email_x0020_Transactional_x0020_Enabled` | |
| SMS Servicing Enabled | Yes/No | `SMS_x0020_Servicing_x0020_Enabled` | |
| Email Servicing Enabled | Yes/No | `Email_x0020_Servicing_x0020_Enabled` | |

---

### 6. Job Addresses

**List URL name:** `Job Addresses`

| Display Name | Type | Internal Name | Notes |
|---|---|---|---|
| Title | Text | `Title` | Address reference |
| Status | Choice | `Status` | |
| Full Address | Text | `Full_x0020_Address` | |
| Building/House Number | Text | `Building_x002F_House_x0020_Number` | |
| Street Address | Text | `Street_x0020_Address` | |
| Town/City | Text | `Town_x002F_City` | |
| Region/County | Text | `Region_x002F_County` | |
| Postcode | Text | `Postcode` | |
| Email | Text | `Email` | ✓ used in oil-lpg-contacts |
| Access Notes | Multi-line | `Access_x0020_Notes` | |
| Install Name | Text | `Install_x0020_Name` | ✓ used in oil-lpg-contacts |
| Install Phone | Text | `Install_x0020_Phone` | ✓ used in oil-lpg-contacts |
| Service Due | Date/Time | `Service_x0020_Due` | |
| Scheduling Notes | Multi-line | `Scheduling_x0020_Notes` | |
| Customer | Lookup → Customers | `Customer` / `CustomerId` | |
| Archive | Yes/No | `Archive` | |
| Priority | Choice | `Priority` | |
| ID Val | Number | `ID_x0020_Val` | |
| SMS Reminders Enabled | Yes/No | `SMS_x0020_Reminders_x0020_Enabled` | |
| Email Reminders Enabled | Yes/No | `Email_x0020_Reminders_x0020_Enabled` | |
| SMS Marketing Enabled | Yes/No | `SMS_x0020_Marketing_x0020_Enabled` | |
| Email Marketing Enabled | Yes/No | `Email_x0020_Marketing_x0020_Enabled` | |
| SMS Transactional Enabled | Yes/No | `SMS_x0020_Transactional_x0020_Enabled` | |
| Email Transactional Enabled | Yes/No | `Email_x0020_Transactional_x0020_Enabled` | |
| SMS Servicing Enabled | Yes/No | `SMS_x0020_Servicing_x0020_Enabled` | |
| Email Servicing Enabled | Yes/No | `Email_x0020_Servicing_x0020_Enabled` | |

---

### 7. Appliances

**List URL name:** `Appliances` — ~4,489 items (approaching 5,000 threshold — index filtered columns)

| Display Name | Type | Internal Name | Notes |
|---|---|---|---|
| Title | Text | `Title` | Appliance identifier |
| Appliance Type | Text | `Appliance_x0020_Type` | |
| Appliance Make | Text | `Appliance_x0020_Make` | |
| Appliance Model | Text | `Appliance_x0020_Model` | |
| Appliance Location | Text | `Appliance_x0020_Location` | |
| Service Due | Date/Time | `Service_x0020_Due` | |
| Fuel Type | Choice | `Fuel_x0020_Type` ✓ | Domestic Gas, Commercial Gas, LPG, Oil, ASHP, Unvented Cylinder, Miscellaneous. Default: Domestic Gas |
| Serial Number | Text | `Serial_x0020_Number` | |
| Gas Council Number | Text | `Gas_x0020_Council_x0020_Number` | |
| Notes | Multi-line | `Notes` | |
| Scheduling Notes | Multi-line | `Scheduling_x0020_Notes` | |
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
