# Use cases
---
### UC-01: Execute Manual Wealth Scan
| Field | Description |
| :--- | :--- |
| **Actor** | Authenticated User |
| **Preconditions** | User is logged in via OAuth2; Rate limit (30 min) is not active. |
| **Main Flow** | 1. User triggers "Stash Refresh" for a league. <br> 2. `WealthSyncManager` validates the rate limit. <br> 3. `ValuationService` calls `PoeApiService` to fetch items from GGG. <br> 4. `PoeApiService` fetches all items. <br> 5. PoE API calls `SmartFilter` and marks high-variance items. <br> 4. `PoeApiService` returns items to `ValuationService`. <br> 4. `ValuationService` prices bulk items via PoE Ninja (`PoeNinjaApiService`) and marked items via PoE Trade (`PoeTradeApiService`). <br> 5. System persists a new `StashSnapshot` with `TabSnapshots`. |
| **Post-condition** | Stash history and dashboard are updated. |
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
---
### UC-05 Execute Character Equipment Scan
| Field | Description |
| :--- | :--- |
| **Actor** | Authenticated User |
| **Preconditions** | 1. User is authenticated. <br> 2. A valid league has been selected. <br> 3. User is navigating the Character section. <br> 4. The account has at least one character in the selected league. |
| **Main Flow** | 1. User selects a character from the league list. <br> 2. `PoeApiService` fetches the character's inventory data. <br> 3. `ValuationService` prices each equipped item (most will require `PoeTradeApiService` due to being Rare/Unique gear). <br> 4. System persists a `CharacterSnapshot` linked to all `SnapshotItems`. |
| **Post-condition** | Character-specific investment history is updated. |
