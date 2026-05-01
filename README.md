# jax-plumbing-automations

Power Automate flow definitions and documentation for Jax Plumbing & Heating.

## Overview

This repo stores all Power Automate flow definitions as version-controlled JSON. It is separate from the Power App repo ([jax-plumbing-powerapps](https://github.com/plutojal/jax-plumbing-powerapps)) which handles the frontend application.

## Repo structure

```
jax-plumbing-automations/
├── flows/
│   ├── mot-monthly-alert/
│   │   ├── definition.json         # Flow definition
│   │   ├── mot-monthly-alert.zip   # Ready-to-import Power Automate package
│   │   └── README.md               # Flow documentation
│   └── qualification-expiry-alert/
│       ├── definition.json
│       ├── qualification-expiry-alert.zip
│       └── README.md
└── README.md
```

## SharePoint context

All flows connect to the following SharePoint lists:

| List | Purpose |
|---|---|
| Staff | Master list of all employees |
| Qualification Types | Master list of 48 qualifications with codes, renewal periods, providers |
| Qualification Records | One record per person per qualification — linked to Staff and Qualification Types |
| Vehicles | Company vehicle fleet with MOT, tax, and service dates |

### Staff list — titles (exact casing for lookups)

| Name | Type |
|---|---|
| Cameron Collings | Engineer |
| Charlotte Cheshire | Office |
| Dan Markwell | Office |
| Danny Coston | Engineer |
| Dave Freeman | Subcontractor |
| Emma Buchan | Office |
| George Buchan | Engineer |
| Harley Rix | Engineer |
| Ian Moore | Subcontractor |
| Jack French | Engineer |
| James Lewis | Subcontractor |
| John Bowen | Engineer |
| Karl Batterbee | Subcontractor |
| Katrina French | Office |
| Kerrie-Ann French | Office |
| Kieran Baldry | Office |
| Kobie Jones | Engineer |
| Lewis Gammon | Engineer |
| Matty Greaves | Engineer |
| Roger Cadd | Engineer |
| Roman Walding | Engineer |
| Ryan Brennand | Engineer |
| Simon Cheshire | Subcontractor |

### Qualification Records columns

| Column | Type | Notes |
|---|---|---|
| Title | Text | `Staff Name - Qualification Name` — unique identifier |
| Staff | Lookup → Staff | Exact match to Staff Title |
| Qualification Name | Lookup → Qualification Types | Drives Code, Renewal, Provider via lookup |
| Start Date | Date | MM/DD/YYYY |
| Expiry Date | Date | MM/DD/YYYY |
| Certificate Link | Hyperlink | Link to PDF in document library |
| Notes | Text | Free text |

### Vehicles columns

| Column | Type | Notes |
|---|---|---|
| Title | Text | Registration plate — unique identifier |
| Registration | Text | |
| Driver | Lookup → Staff | Exact match to Staff Title |
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

## Flow naming convention

`[trigger]-[subject]-[action]`

Examples:
- `mot-monthly-alert`
- `qualification-expiry-alert`
- `tax-monthly-alert`

## How to export a flow from Power Automate

1. Open Power Automate → My Flows
2. Select the flow → click the three dots → Export → Package (.zip)
3. Unzip the package
4. Copy the flow JSON from `Microsoft.Flow/flows/` into the relevant `definition.json` in this repo
5. Commit with a message like: `update: mot-monthly-alert — changed threshold to 60 days`

## How to import a flow into Power Automate

1. Open Power Automate → My Flows → Import → Import Package (Legacy)
2. Download the relevant `.zip` from this repo and upload it
3. Select your existing SharePoint and Outlook connections when prompted
