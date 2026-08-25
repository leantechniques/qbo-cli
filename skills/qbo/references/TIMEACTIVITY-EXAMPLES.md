# TimeActivity Examples

TimeActivity is registered as a generic entity (queryable, creatable, updatable, deletable), so it works through the standard verbs — no dedicated command.

## Read

```bash
# List time activities for a date range
qbo list timeactivity --where "TxnDate >= '2026-08-01' AND TxnDate <= '2026-08-24'" --json

# Fetch one by ID
qbo get timeactivity 145

# Filter by employee, project useful fields
qbo query "SELECT * FROM TimeActivity WHERE EmployeeRef = '55'" --select Id,TxnDate,Hours,Minutes,Description
```

## Create

JSON via file or stdin (`-f -`):

```bash
echo '{
  "TxnDate": "2026-08-24",
  "NameOf": "Employee",
  "EmployeeRef": {"value": "55"},
  "CustomerRef": {"value": "102"},
  "ItemRef": {"value": "3"},
  "BillableStatus": "Billable",
  "Hours": 4,
  "Minutes": 30,
  "Description": "Sprint planning and backlog grooming"
}' | qbo create timeactivity -f -

# Dry-run first to see what would be sent
qbo create timeactivity -f entry.json --dry-run
```

Key fields:

- `NameOf` — `"Employee"` or `"Vendor"`; pair with `EmployeeRef` or `VendorRef` accordingly.
- Duration — either `Hours`/`Minutes`, or `StartTime`/`EndTime` (ISO 8601).
- `BillableStatus` — `Billable`, `NotBillable`, or `HasBeenBilled`. Billable entries need `CustomerRef` and typically `HourlyRate`.
- `ItemRef` — service item; required when billable to a customer.

## Update / Delete

```bash
qbo update timeactivity 145 -f patch.json --sparse
qbo delete timeactivity 145
```

Always include `Id` and `SyncToken` in the update payload. `qbo update` defaults to a FULL update — any writable field omitted from the payload gets cleared. Pass `--sparse` to update only the fields present in the payload.
