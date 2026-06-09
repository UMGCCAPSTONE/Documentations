# T-3: User Stories and Acceptance Criteria

**Sprint:** Sprint 1
**Labels:** `scope` `planning`

---

## Roles

Roles align with the `UserRole` enum: **OWNER**, **MANAGER**, **STAFF**.

> **Note:** Role-based access gating is **out of MVP scope**. The mockups show a single Google sign-in with no per-role restrictions. Role labels in the stories below indicate the *typical actor*, not an enforced permission boundary. All authenticated users can access all MVP features regardless of role.

- **Manager** — primary user, responsible for inventory oversight, purchasing, and menu decisions
- **Staff** — uses the app to check stock and flag items
- **Owner** — reviews reports and high-level metrics

---

## Authentication

> **Note:** Authentication uses Google Sign-In via Firebase (ADR-0003). Email/password login is out of MVP scope.

### US-AUTH-1
> As a **Manager**, I want to sign in with my Google account so that I can access the application without managing a separate password.

**Acceptance Criteria:**
- Login page renders a single "Continue with Google" button (no email/password fields)
- Clicking the button opens the Firebase/Google OAuth flow
- Successful sign-in redirects to the Dashboard
- Failed or cancelled sign-in returns the user to the login page with an appropriate message
- Session persists across page refreshes until logout

### US-AUTH-2
> As a **Manager**, I want to sign out of the application so that my account remains secure when I step away.

**Acceptance Criteria:**
- A sign-out action is accessible from all authenticated pages (e.g., nav or profile menu)
- Signing out clears the Firebase session and redirects to the login page
- After sign-out, navigating back does not restore the authenticated session

### US-AUTH-3
> As a **first-time user**, I want to be prompted to enter my restaurant name and select my role after signing in with Google so that my profile is set up before I reach the app.

**Acceptance Criteria:**
- After a first-time Google sign-in, the user is redirected to an onboarding screen before the Dashboard
- Onboarding screen collects `restaurantName` and `role` (maps to `UserProfile.restaurantName` / `UserProfile.role`)
- Both fields are required before the user can proceed
- Submitting the form saves the profile and redirects to the Dashboard
- Returning users who have already completed onboarding skip this screen entirely
- Relates to T-5

### US-AUTH-4
> As any **authenticated user**, I want unauthenticated routes to redirect to the login page so that private data cannot be accessed without credentials.

**Acceptance Criteria:**
- Accessing a protected route while unauthenticated redirects to the login page
- After a successful sign-in, the user is redirected to the originally requested page (or the Dashboard)

---

## Dashboard

### US-DASH-1
> As a **Manager**, I want to see a summary of key inventory metrics when I open the app so that I can quickly assess the current state of my stock.

**Acceptance Criteria:**
- Dashboard displays: total SKUs, expiring within 48h count, at-risk value, and below-par item count (per `DashboardSummary` contract)
- Metrics load from the API on page arrival
- A loading indicator is shown while data is fetching
- An error message is shown if the API call fails

### US-DASH-2
> As a **Manager**, I want to see urgent inventory alerts (low-stock and expiring items) on the Dashboard so that I can take action before stock runs out or food goes to waste.

**Acceptance Criteria:**
- Alerts section lists items that are low in stock or expiring soon
- Each alert shows the item name, current quantity or expiry date, and a visual severity indicator
- Clicking an alert navigates to or highlights the relevant item in the Inventory page
- Empty state is shown when there are no active alerts

### US-DASH-3
> As a **Manager**, I want to see a preview of AI-generated menu recommendations on the Dashboard so that I can immediately act on opportunities to reduce waste.

**Acceptance Criteria:**
- Up to 3 recommendation preview cards are shown on the Dashboard
- Each card shows the dish name and a one-line summary
- A CTA button navigates to the full Menu Builder page
- Loading and error states are handled

---

## Inventory Management

### US-INV-1
> As a **Manager**, I want to view all inventory items in a searchable, sortable table so that I can quickly find and assess any item in my stock.

**Acceptance Criteria:**
- Table displays: name, category, quantity, unit, par level, expiry date, unit cost, and status (low-stock flag is driven by quantity vs. parLevel)
- Search input filters the table in real time (or on submit)
- At least name and expiry date columns are sortable
- Loading, empty, and error states are all handled

### US-INV-2
> As a **Manager**, I want to see inventory KPI cards at the top of the Inventory page so that I know the aggregate health of my stock without reading every row.

**Acceptance Criteria:**
- Cards display: total items, SKU count (aggregate), items expiring soon, at-risk value, and below-par count
- Data is fetched on page load and updates after CRUD operations

### US-INV-3
> As a **Manager**, I want to add a new inventory item so that I can keep my stock list up to date when new products arrive.

**Acceptance Criteria:**
- "Add Item" opens a form/modal with all required fields (name, category, quantity, unit, par level, expiry date, unit cost)
- All required fields are validated before submission
- Successful creation adds the item to the table without a full page reload
- An API error is surfaced with a descriptive message

### US-INV-4
> As a **Manager**, I want to edit an existing inventory item so that I can correct quantities, costs, or expiry dates as things change.

**Acceptance Criteria:**
- Clicking "Edit" on a row opens the form pre-populated with the item's current data, including par level
- All fields are validated before submission
- Successful update is reflected in the table immediately, including any change to the below-par status
- An API error is surfaced with a descriptive message

### US-INV-5
> As a **Manager**, I want to delete an inventory item so that I can remove discontinued or erroneous entries.

**Acceptance Criteria:**
- Clicking "Delete" shows a confirmation prompt with the item name
- Confirming removes the item from the table without a full page reload
- Cancelling closes the prompt with no changes made
- If the item is used by one or more non-archived menu items, deletion is blocked (API returns 409 `ITEM_IN_USE`) and the user is shown the list of dishes that reference it (per ADR-0001)

### US-INV-6 *(Stretch)*
> As a **Manager**, I want to export my inventory to a CSV file so that I can share or back up the data outside the application.

**Acceptance Criteria:**
- Export button downloads a correctly formatted CSV of all current inventory items
- Column headers match the displayed table fields

### US-INV-7 *(Stretch)*
> As a **Manager**, I want to import inventory items from a CSV file so that I can bulk-load stock data instead of entering items one by one.

**Acceptance Criteria:**
- Import accepts a CSV in the expected format
- Valid rows are created/updated; invalid rows are reported as errors without aborting the full import

---

## Menu Builder / AI Recommendations

### US-MENU-1
> As a **Manager**, I want to generate AI-powered menu recommendations based on my current inventory so that I can reduce waste by turning at-risk ingredients into specials.

**Acceptance Criteria:**
- "Generate Recommendations" button triggers the AI workflow
- A loading state (spinner or skeleton) displays during generation
- Generated recommendations appear as cards upon success
- An error message is shown if generation fails (with an option to retry)

### US-MENU-2
> As a **Manager**, I want to view the details of each AI-generated recommendation so that I understand what dish is suggested, which ingredients it uses, and why it was recommended.

**Acceptance Criteria:**
- Each recommendation card shows: dish name, list of ingredients used, and a short explanation from the AI
- A "View Detail / Recipe" action expands or navigates to a detailed view

### US-MENU-3
> As a **Manager**, I want to accept a recommendation so that I can add it to today's specials and signal to the AI that it was useful.

**Acceptance Criteria:**
- "Accept" action calls the appropriate API endpoint
- Accepted recommendation is visually marked as accepted
- A success toast or notification confirms the action
- An error message is shown if the action fails

### US-MENU-4
> As a **Manager**, I want to dismiss a recommendation so that it is removed from my active list when it is not relevant.

**Acceptance Criteria:**
- "Dismiss" action calls the appropriate API endpoint
- Dismissed recommendation is removed from or visually de-emphasized in the list
- A success notification confirms the action

### US-MENU-5
> As a **Manager**, I want to save a recommendation for later so that I can revisit it without committing to it right away.

**Acceptance Criteria:**
- "Save" action calls the appropriate API endpoint
- Saved recommendation is marked as saved
- A success notification confirms the action

### US-MENU-6
> As a **Manager**, I want the application to degrade gracefully when the AI service is unavailable so that the rest of the app remains usable.

**Acceptance Criteria:**
- When the LLM API is unreachable or returns an error, a user-friendly error message is shown
- The application does not crash or display a blank/broken screen
- Other pages and features remain accessible

---

## Suppliers

### US-SUPP-1
> As a **Manager**, I want to view a list of all my suppliers so that I can quickly look up contact and delivery information.

**Acceptance Criteria:**
- Supplier table renders with: name, contact name, email/phone, and delivery cadence
- Data is fetched from the API on page load
- Loading, empty, and error states are handled
- Basic search or filter by name is available

### US-SUPP-2
> As a **Manager**, I want to add a new supplier so that I can record vendors I work with as my supplier network grows.

**Acceptance Criteria:**
- "Add Supplier" opens a form with all required fields (name, contact, email, phone, delivery cadence)
- All required fields are validated before submission
- Successful creation adds the supplier to the directory
- An API error is surfaced with a descriptive message

### US-SUPP-3
> As a **Manager**, I want to edit an existing supplier's information so that I can keep contact details up to date.

**Acceptance Criteria:**
- Clicking "Edit" opens the supplier form pre-populated with current data
- Successful update is reflected in the directory immediately
- An API error is surfaced with a descriptive message

### US-SUPP-4 *(Stretch)*
> As a **Manager**, I want to view a supplier's order history so that I can track past purchases and spending.

> **Note:** Order history, spend analytics, and upcoming deliveries are explicitly out of MVP scope per the design doc. Matches T-9S — only implement if all core MVP tickets are complete.

**Acceptance Criteria:**
- Supplier detail view shows a list of historical orders with date, items, and amount
- Section handles empty and error states

---

## Reports

### US-REP-1
> As an **Owner**, I want to see high-level KPI cards on the Reports page so that I can quickly understand the financial health of my inventory.

**Acceptance Criteria:**
- Cards display: total inventory value, low-stock item totals, expiring item counts, and waste-risk summary
- Data is fetched on page load
- Loading and error states are handled

### US-REP-2
> As an **Owner**, I want to see inventory data broken down by category so that I can identify which product groups are over- or under-stocked.

**Acceptance Criteria:**
- Category summary table renders with correct columns (category, item count, total value, low-stock count)
- Table is sortable by at least one column
- Loading, empty, and error states are handled

### US-REP-3
> As a **Manager**, I want to see a history of AI recommendations and their outcomes (accepted/dismissed/saved) so that I can evaluate the usefulness of the AI feature over time.

**Acceptance Criteria:**
- Recommendation history section lists past recommendations with status and date
- Section handles empty and error states

### US-REP-4
> As a **Manager**, I want to see a waste-risk summary on the Reports page so that I can proactively address items that are likely to expire before use.

**Acceptance Criteria:**
- Waste-risk section lists items at risk, with expiry date and estimated value at risk
- Section handles empty and error states

### US-REP-5 *(Stretch)*
> As an **Owner**, I want to filter reports by a date range so that I can review performance for a specific period.

**Acceptance Criteria:**
- Date-range filter updates all relevant charts and tables on the Reports page
- Selecting a range and applying it fetches new data from the API
- Charts and tables are correctly labeled with the active date range

---

## Story Count Summary

| Area                     | Core Stories | Stretch Stories |
|--------------------------|:------------:|:---------------:|
| Authentication           | 4            | 0               |
| Dashboard                | 3            | 0               |
| Inventory Management     | 5            | 2               |
| Menu Builder / AI        | 6            | 0               |
| Suppliers                | 3            | 1               |
| Reports                  | 4            | 1               |
| **Total**                | **25**       | **4**           |
