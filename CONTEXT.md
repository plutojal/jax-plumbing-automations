# CONTEXT.md — Jax Plumbing & Heating automations

This file provides full project context for Claude Code sessions.
Read this file first before making any changes to the repo.

---

## Project overview

Jax Plumbing & Heating are building a SharePoint-based system to manage:
- Staff qualifications and renewal tracking
- Company vehicle fleet (MOT, tax, service dates)
- Automated email alerts via Power Automate

This repo (`jax-plumbing-automations`) stores Power Automate flow definitions.
The Power App frontend lives in a separate repo: https://github.com/plutojal/claude_code_jax_plumbing

---

## SharePoint lists

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

## Document library

**Name:** Training Certificates
**Structure:** `Employee Name / Qualification Name / Certificate_Date.pdf`
Each Qualification Record has a Certificate Link column pointing to the relevant PDF.

---

## Power Automate flows

### mot-monthly-alert
- **Trigger:** Recurrence — 1st of every month, 08:00
- **Source list:** Vehicles
- **Filter:** MOT Due Date ≤ today + 30 days, MOT Due Date not empty
- **Sort:** MOT Due Date ascending
- **Email to:** admin — subject: `MOT Alert — [X] vehicle(s) due within 30 days`
- **Email body:** Table — Registration, Driver, Make, Model, MOT Due Date
- **Status:** Not yet built

### qualification-expiry-alert
- **Trigger:** Recurrence — 1st of every month, 08:00
- **Source list:** Qualification Records
- **Filter:** Expiry Date ≤ today + 30 days, Expiry Date not empty
- **Sort:** Expiry Date ascending, then Staff A–Z
- **Group by:** Staff (in email body)
- **Email to:** admin — subject: `Qualification Alert — [X] qualification(s) expiring within 30 days`
- **Email body:** Table grouped by staff — Staff, Qualification Name, Expiry Date
- **Status:** Not yet built

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
