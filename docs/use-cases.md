# Use cases
---
### UC-01: Execute Manual Wealth Scan
| Field | Description |
| :--- | :--- |
| **Actor** | Authenticated User |
| **Preconditions** | User is logged in via OAuth2; Rate limit (30 min) is not active. |
| **Main Flow** | 1. User clicks "Refresh Wealth" and provides the `league_id`. <br> 2. `WealthSyncManager` validates the rate limit. <br> 3. `ValuationService` fetches items from GGG and prices from poe.ninja. <br> 4. System identifies the most valuable item and captures its JSON metadata. <br> 5. System persists a new `Snapshot` with `TabSnapshots`. |
| **Post-condition** | Dashboard and History charts are updated with new data points. |
---
### UC-02: View Wealth Evolution (Analytics)
| Field | Description |
| :--- | :--- |
| **Actor** | Authenticated User |
| **Preconditions** | 1. User is authenticated. <br> 2. At least one successful `Snapshot` (UC-01) exists for the currently selected league. |
| **Main Flow** | 1. User navigates to the "History" tab. <br> 2. Frontend requests getHistory() from `StashController`. <br> 3. `ValuationService` retrieves all `Snapshots` + `TabSnapshots` for the User ID. <br> 4. Data is sorted by timestamp and sent to the UI. |
| **Post-condition** | The user sees a Line Chart of total wealth and a stacked area chart for tab distribution. |
---
### UC-03: Inspect Historical "Best Drop"
| Field | Description |
| :--- | :--- |
| **Actor** | Authenticated User |
| **Preconditions** | 1. User is viewing a specific data point in the History Chart. <br> 2. A `SnapshotItem` was successfully persisted during that specific scan. |
| **Main Flow** | 1. User clicks on a specific data point in the history chart. <br> 2. System retrieves the associated `SnapshotItem`. <br> 3. System parses the `raw_properties` JSON blob. <br> 4. UI renders a high-fidelity item tooltip (similar to the in-game UI). |
| **Post-condition** | User sees the exact mods and rolls of their most valuable item at that point in time. |
---
### UC-04 Select Active League
| Field | Description |
| :--- | :--- |
| **Actor** | Authenticated User |
| **Preconditions** | 1. User is successfully authenticated via OAuth2. <br> 2. The system has a valid `access_token` to fetch the league list from GGG. |
| **Main Flow** | 1. User opens the League selector. <br> 2. System fetches the list of active leagues from GGG API. <br> 3. User selects a league (e.g., "Settlers"). <br> 4. System updates the dashboard and history view to show data only for that league. |
| **Post-condition** | All subsequent scans and historical views are filtered by the selected league. |
