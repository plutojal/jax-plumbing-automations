# Claude Code — Project Guide

**Read `CONTEXT.md` first.** It contains the full project overview, all SharePoint list schemas, staff names, and flow inventory.

---

## Repo structure

```
flows/
├── <flow-name>/          # recurring scheduled flows
│   ├── definition.json
│   ├── <flow-name>.zip   # ready-to-import Power Automate package
│   └── README.md
└── one-off/              # manually triggered, run-once flows
    └── <flow-name>/
```

---

## SharePoint sites

| Key | URL |
|---|---|
| JaxPlumbing | `https://jaxplumbinggy.sharepoint.com/sites/JaxPlumbing` |
| CRM Hub | `https://jaxplumbinggy.sharepoint.com/sites/JaxPlumbingCRMHub-Live` |

CRM lists (Customers, Job Addresses, Appliances, Jobs, Appointments…) live on **CRM Hub**.
Planner backups and general documents live on **JaxPlumbing**.

---

## Power Automate — technical conventions

### Connection reference keys

| Connector | Key in `connectionReferences` | Use in `host.connectionName` |
|---|---|---|
| SharePoint | `shared_sharepointonline` | `"shared_sharepointonline"` |
| Planner | `shared_planner` | `"shared_planner"` |

`host.connectionName` must always be the **key** (e.g. `"shared_planner"`), never the full connection GUID.

### Action format — OpenApiConnection

```json
{
  "type": "OpenApiConnection",
  "inputs": {
    "host": {
      "apiId": "/providers/Microsoft.PowerApps/apis/shared_planner",
      "connectionName": "shared_planner",
      "operationId": "ListTasks_V3"
    },
    "parameters": { ... },
    "authentication": "@parameters('$authentication')"
  }
}
```

### runAfter — always Succeeded only

```json
"runAfter": { "Previous_Action": ["Succeeded"] }
```

Never use `["Succeeded", "Failed"]` unless there is a deliberate reason to continue on failure.

### Action name limit

**80 characters maximum.** Names with spaces use underscores. Always verify with `len(name) <= 80`.

### SharePoint column internal names

Spaces in display names become `_x0020_`, forward slash `/` becomes `_x002F_`.

Examples: `Fuel Type` → `Fuel_x0020_Type`, `Town/City` → `Town_x002F_City`

Lookup columns return both a display value and a numeric ID:
- `Customer` → display value, `CustomerId` → numeric ID for Get Item calls
- `Job_x0020_Address` → display value, `Job_x0020_AddressId` → numeric ID

### Pagination for large lists

Lists over ~100 items need pagination enabled on Get Items:

```json
"runtimeConfiguration": {
  "paginationPolicy": { "minimumItemCount": 5000 }
}
```

### ZIP package structure

Every importable flow package must contain:
```
manifest.json
Microsoft.Flow/flows/manifest.json
Microsoft.Flow/flows/<FLOW_GUID>/definition.json
Microsoft.Flow/flows/<FLOW_GUID>/connectionsMap.json
Microsoft.Flow/flows/<FLOW_GUID>/apisMap.json
```

---

## Git push

The remote requires PAT authentication:

```bash
git push https://<PAT>@github.com/plutojal/jax-plumbing-automations.git HEAD:main
git fetch https://<PAT>@github.com/plutojal/jax-plumbing-automations.git main:refs/remotes/origin/main
