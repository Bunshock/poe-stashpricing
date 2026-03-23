## Product Vision

Provide Path of Exile players with a centralized tool to value their total wealth (Stash + Inventory) by cross-referencing real-time market data (poe.ninja), enabling historical tracking of their net worth.

---

### Functional Requirements (FR)

- **FR01**: The system must authenticate users via OAuth2 using the official GGG (Grinding Gear Games) service.

- **FR02**: The system must fetch and list all available stash tabs for the authenticated user.

- **FR03**: The system must calculate the total value of items in Chaos Orbs and Divine Orbs.

- **FR04**: The system must persist a historical record (timestamp + total value) for each scan.

- **FR05**: The system must identify and display the top 5 most valuable items in the account.

- **FR06**: The system must perform a "Deep Copy" of the most valuable item's metadata (JSON) during each scan.

- **FR07**: The system must provide a REST endpoint to retrieve historical wealth data grouped by stash tab names.

- **FR08**: The system must distinguish between Stash wealth and Character wealth to avoid data inflation when swapping gear.

- **FR09**: The system must store the Divine/Chaos exchange rate for every snapshot to maintain economic historical accuracy.

- **FR10**: The system must use a "Smart Filter" to prioritize Trade API calls for high-variance items, respecting GGG's rate limits.

### Non-Functional Requirements (NFR)

- **NFR01 (Performance)**: Market prices from poe.ninja must be cached (min. 30 minutes) to reduce API latency.

- **NFR02 (Scalability)**: Data fetching must be asynchronous to handle large JSON payloads without blocking threads.

- **NFR03 (Security)**: The application must not store user passwords; only temporary OAuth2 access tokens should be used.

- **RNF04 (Data Integrity)**: Snapshots must be immutable once created; they represent a "frozen" moment in the account's history.

- **RNF05 (Storage)**: The system must use a JSONB or equivalent binary-large-object column to ensure affix data is preserved regardless of future game updates. This follows the fact that Path of Exile's items have a "Schema-less" nature (an item can have 1 mod or 10 mods).

- **RNF06 (Data Model)**: The persistence layer must use an optional foreign key (nullable FK) strategy for SnapshotItems to allow polymorphic reuse between Stash and Character snapshots.

- **RNF07 (Performance)**: Pricing for bulk items must be resolved in $O(1)$ using an in-memory price cache refreshed at the start of the valuation cycle.

---

### User Stories

- **US01** - **Secure Auth**: As a user, I want to log in using my PoE account securely so that the app can read my data without compromising my credentials.
    - ***Acceptance Criteria 1***: The system must redirect the user to pathofexile.com for authentication.
    - ***Acceptance Criteria 2***: The system must receive and securely store a temporary access_token.
    - ***Acceptance Criteria 3***: The system must identify the user's account name to personalize the session.

- **US02** - **Wealth Analytics**: As a user, I want to see a graphical breakdown of my stash value (Pie/Treemap) and its evolution over time (Line/Stacked Chart), so I can identify income sources and farming efficiency per league
    - ***Acceptance Criteria 1***: Includes league selection, price formatting in Chaos (Divine) units, per-tab filtering, and 'Top N' grouping logic to handle accounts with a high volume of stash tabs.

- **US04** - **Character Progression**: As a user, I want to track the value of my equipped gear for a specific character, so I can see how much I've invested in my build.
    - ***Acceptance Criteria 1***: Must allow selecting a character from the active league and list all equipped items with their individual prices.

- **US05** - **Item Tooltip Inspection**: As a user, I want to see the full affixes and metadata of my most expensive drops or equipped gear in any past snapshot, so I can review the exact state of my items at that time.
    - ***Acceptance Criteria 1***: Must render a tooltip using the stored `jsonb` metadata.

- **US06** - **Manual Refresh**: As a user, I want to trigger a manual scan of my stash so that I can see the impact of my recent trades immediately.
    - ***Acceptance Criteria 1***: The "Refresh" button must be disabled if the 30-minute rate limit is active.
    - ***Acceptance Criteria 2***: Upon clicking, a loading indicator must appear while the backend fetches data from GGG and poe.ninja.
    - ***Acceptance Criteria 3***: On success, the dashboard must update immediately without a full page reload.

- **US07** - **Economy Context**: As a user, I want to see the Divine/Chaos ratio at the time of any past snapshot.
    - ***Acceptance Criteria 1***: Historical charts must display the conversion rate used for that specific data point.
