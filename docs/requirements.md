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

### Non-Functional Requirements (NFR)

- **NFR01** (Performance): Market prices from poe.ninja must be cached (min. 30 minutes) to reduce API latency.

- **NFR02** (Scalability): Data fetching must be asynchronous to handle large JSON payloads without blocking threads.

- **NFR03** (Security): The application must not store user passwords; only temporary OAuth2 access tokens should be used.

- **RNF04** (Data Integrity): Snapshots must be immutable once created; they represent a "frozen" moment in the account's history.

- **RNF05** (Storage): The system must use a JSONB or equivalent binary-large-object column to ensure affix data is preserved regardless of future game updates. This follows the fact that Path of Exile's items have a "Schema-less" nature (an item can have 1 mod or 10 mods).

---

### User Stories

- **US01** - **Secure Auth**: As a user, I want to log in using my PoE account securely so that the app can read my data without compromising my credentials.
    - ***Acceptance Criteria 1***: The system must redirect the user to pathofexile.com for authentication.
    - ***Acceptance Criteria 2***: The system must receive and securely store a temporary access_token.
    - ***Acceptance Criteria 3***: The system must identify the user's account name to personalize the session.

- **US02** - **Wealth Dashboard**: As a user, I want to see a pie chart of my wealth per stash tab so that I can identify where my currency is concentrated.
    - ***Acceptance Criteria 1***: The user must be able to select a League (e.g., Standard, Hardcore, Current League) from a dropdown.
    - ***Acceptance Criteria 2***: The dashboard must display a pie chart showing the percentage of total wealth held in each stash tab.
    - ***Acceptance Criteria 3***: Hovering over a pie slice must show the absolute value in Chaos Orbs.
    - ***Acceptance Criteria 4***: The visualization must handle accounts with more than 10 tabs using a "Top N" grouping or a scrollable Treemap.
    - ***Acceptance Criteria 5***: Each data point (slice/bar/rectangle) must show the absolute value in the format: X Chaos (Y Divine).

- **US03** - **Value Tracking**: As a user, I want to see a line chart of my net worth over time so that I can track my farming efficiency.
   - ***Acceptance Criteria 1***: The line chart must allow filtering by League (you cannot mix Standard and League wealth in one line).
    - ***Acceptance Criteria 2***: The X-axis must represent time (Date/Time) and the Y-axis must represent Total Value.
    - ***Acceptance Criteria 3***: The chart must support "Divine Orb" as a secondary unit of measurement.

- **US04** - **Manual Refresh**: As a user, I want to trigger a manual scan of my stash so that I can see the impact of my recent trades immediately.
    - ***Acceptance Criteria 1***: The "Refresh" button must be disabled if the 30-minute rate limit is active.
    - ***Acceptance Criteria 2***: Upon clicking, a loading indicator must appear while the backend fetches data from GGG and poe.ninja.
    - ***Acceptance Criteria 3***: On success, the dashboard must update immediately without a full page reload.

- **US05** - Item Hall of Fame: As a user, I want to see the full details (affixes, rolls) of my most expensive item per scan, so I can remember my "big drops" exactly as they were.
    - ***Acceptance Criteria 1***: The system must capture the JSON metadata of the top item and store it permanently.

- **US06** - Financial Drill-down: As a user, I want to filter my wealth history by specific tabs, so I can see which farming method (e.g., Essence vs. Mapping) is most profitable.
    - ***Acceptance Criteria 1***: The history chart must allow toggling between "Total Value" and individual "Tab Values."