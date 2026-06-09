# One-Off: Export Oil & LPG Appliance Contacts

Manually triggered. Queries the Appliances list for all records where `Fuel Type` is **Oil** or **LPG**, then enriches each record with contact details from the linked Customer and Job Address, and saves a CSV file to SharePoint.

> **One-off use only.** This flow has a manual trigger — run it once, download the file, then disable or delete it.

## Logic

```
Manual Trigger
    └── Initialize Results array
          └── Get Appliances (filter: Fuel Type = Oil or LPG)
                └── For each appliance (sequential):
                      ├── Get Customer record     ─┐ (parallel)
                      ├── Get Job Address record  ─┘
                      └── Append row to Results array
                └── Create CSV from Results
                └── Save CSV to SharePoint
```

## Output Columns

| Column | Source |
|---|---|
| Appliance Title | Appliances → `Title` |
| Appliance Type | Appliances → `Appliance Type` |
| Fuel Type | Appliances → `Fuel Type` |
| Customer Name | Appliances → `Customer` (lookup display) |
| Job Address | Appliances → `Job Address` (lookup display) |
| Customer Landline | Customers → `Landline` |
| Customer Mobile | Customers → `Mobile` |
| Customer Email | Customers → `Email` |
| Job Address Email | Job Addresses → `Email` |
| Install Name | Job Addresses → `Install Name` |
| Install Phone | Job Addresses → `Install Phone` |

## Output File

| Property | Value |
|---|---|
| Site | `https://jaxplumbinggy.sharepoint.com/sites/JaxPlumbingCRMHub-Live` |
| Folder | `/Shared Documents/Data Exports` |
| Filename | `oil_lpg_contacts_YYYY-MM-DD.csv` |

> **Note:** The `Data Exports` folder must exist in Shared Documents before the flow runs, or update the folder path in `Create_File_-_Oil_LPG_Contacts`. The file is a CSV which opens directly in Excel — use File → Save As to save as `.xlsx` if needed.

## SharePoint Lists Used

| List | Site | Purpose |
|---|---|---|
| Appliances | JaxPlumbingCRMHub-Live | Source — filtered by Fuel Type |
| Customers | JaxPlumbingCRMHub-Live | Landline, Mobile, Email |
| Job Addresses | JaxPlumbingCRMHub-Live | Email, Install Name, Install Phone |

## Connection

Requires the **SharePoint** connection authenticated as `data@jaxplumbing.co.uk`.

## Notes

- The Appliances list has ~4,489 items. Pagination is enabled (`minimumItemCount: 5000`) so all matching records are returned in one call.
- If the `Fuel Type` column is not indexed in SharePoint, the OData filter may fail with a list view threshold error. Index it via List Settings → Indexed Columns if needed.
- If an appliance has a missing Customer or Job Address lookup, the Get Item step for that record will fail. The Append step still runs and writes blank strings for the missing contact fields — the row is not skipped.
- The `Fuel_x0020_Type` internal column name assumes "Fuel Type" was created with a space in the column name. If the OData filter returns no results, check the column's internal name in List Settings.
