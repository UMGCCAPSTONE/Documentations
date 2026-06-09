# T-2: Page-Level Requirements

**Sprint:** Sprint 1 · **Owner:** Product Owner · **Labels:** `scope` `planning`
**Depends on:** T-1 (MVP scope) · **Feeds:** T-33 (contract), T-11 (schema), T-3 (user stories), all UI/API tickets

Per-page requirements for the MVP of *Mise* (Smart Inventory Management & AI Recommendation System). For each page: the data displayed, user actions, API needs, DB needs, and rules. Grounded in the MVP mockups (`UMGC_ProjectDesign_MVP_Simplified`) and the shared contract / ADRs.

> **Source-of-truth note:** API shapes, enums, and metrics follow the `@umgccapstone/contracts` package and the backend ADRs. Derived fields (low-stock, expiring, at-risk, availability) are **computed server-side only** (shared ADR 0004). Response envelope: success `{ data, meta? }`, error `{ error: { code, message, field? } }` (shared ADR 0002).

> **Locked decisions:**
> - **Categories** — fixed, hard-coded enum of 8 values: `PRODUCE, MEAT, SEAFOOD, DAIRY, DRY_GOODS, BEVERAGE, FROZEN, OTHER`. Users select from the list; no add/edit in MVP. *(User-defined categories are stretch: T-35 backend / T-36 frontend.)*
> - **Roles** — no role-based access control in MVP. All authenticated users have the same access; `role` is a profile label only (captured at onboarding, gates nothing).
> - **Dish margin / menu pricing** — stretch, not MVP (the contract has no price field on menu items).
>
> **Open items to confirm:**
> - **Expiring window** (PO) — default is a single `EXPIRING_WINDOW_DAYS = 7` flag, with `<48h` highlighted as urgent (mockups also show watch `3–5d` / stable `>5d` tiers). Confirm the threshold(s).
> - **Menu sections** (PO + contract) — the Menu page filters dishes by course (Starters / Pasta / Mains), which needs a `section` field on `MenuItem` that the contract doesn't have. Confirm adding it (contract + T-15), or drop course filtering for the MVP.

---

## 1. Login

**Route:** `/login` · **Auth:** public

- **Displayed data:** product intro/branding; "Continue with Google" button; Firebase-auth assurance copy; Terms/Privacy links.
- **User actions:** Sign in with **Google** (Firebase). No email/password (out of MVP scope — shared ADR 0003).
- **API needs:** none directly; Firebase issues the ID token. After sign-in the app calls the Users endpoint (see Onboarding).
- **DB needs:** none (auth handled by Firebase; profile created at onboarding).
- **Rules:** unauthenticated access to a protected route redirects here; after sign-in, redirect to Dashboard (or the originally requested page).

## 2. Onboarding (first-time setup)

**Route:** `/onboarding` (shown once, post-Google) · **Auth:** authenticated, not yet onboarded

- **Displayed data:** "Signed in as <email>"; restaurant name field; role field; progress stepper.
- **User actions:** enter restaurant name, choose role, "Finish setup".
- **API needs:** `POST /api/users` (create profile) → redirect to Dashboard. `displayName`/`email`/photo come from the Google account.
- **DB needs:** `UserProfile` (firebaseUid, email, displayName?, restaurantName?, role, onboardedAt?).
- **Rules:** restaurant name + role required; `onboardedAt` set on completion; users with `onboardedAt` skip this screen. `role` is informational only (no access control in MVP).

## 3. Dashboard ("Today")

**Route:** `/` · **Auth:** required

- **Displayed data:**
  - **KPI cards:** Total SKUs (`totalSkus`) · Expiring `<48h` (urgent subset of `expiringSoonCount`) · At-risk value (`atRiskValue`) · Below par (`lowStockCount`).
  - **Urgent alerts:** low-stock + expiring items (name, qty or expiry, value at risk).
  - **AI recommendation preview:** up to 3 cards (dish name + one-line summary) with a live availability flag.
  - **Charts:** inventory snapshot (by item) + category breakdown (donut).
- **User actions:** click an alert → jump to the item in Inventory; click a recommendation → go to Menu Builder.
- **API needs:** `GET /api/dashboard/summary` · `GET /api/dashboard/alerts` · `GET /api/dashboard/recommendations/preview` (read-only).
- **DB needs:** reads InventoryItem, Recommendation, MenuItem.
- **Rules:** all metrics computed server-side; recommendation **content is a snapshot**, availability is **live** (ADR 0004); loading / empty / error states required.

## 4. Inventory

**Route:** `/inventory` · **Auth:** required

- **Displayed data:**
  - **KPI cards:** Total items · Expiring `<48h` (urgent subset of `expiringSoonCount`) · At-risk value (`atRiskValue`) · Below par (`lowStockCount`).
  - **Table columns:** Item (name + short descriptor) · Stock (`quantity` + `unit`) · Expiry (days-left badge) · At-risk (value) · **Par / Reorder** (`parLevel` + cadence) · Supplier · Edit.
  - Category filter chips (the 8 fixed categories); a status/urgency filter; search box; Import/Export CSV controls.
- **User actions:** search; filter by category and by status (`urgent`, `expiring-soon`, `low-stock`); sort (≥ name, expiry); **add**, **edit**, **delete** an item; import/export CSV *(stretch — T-7S)*.
- **API needs:** `GET /api/inventory` (params: `search, category, status, sort, order, page, pageSize`) · `GET /api/inventory/:id` · `POST` · `PATCH /api/inventory/:id` · `DELETE /api/inventory/:id`. KPI summary via `GET /api/dashboard/summary`. CSV: `GET/POST /api/inventory/export|import` *(stretch)*.
- **DB needs:** `InventoryItem` (name, category, quantity, unit, unitCost, parLevel, expirationDate, supplierId?); relation to Supplier (nullable) and to MenuItemIngredient.
- **Rules:**
  - Validation: name required; `quantity ≥ 0`; `unitCost ≥ 0`; valid `expirationDate`; `category` ∈ the fixed 8-value enum; bad input → 400 with `field`.
  - Derived (server): `isLowStock` (qty ≤ parLevel), `isExpiringSoon`, `atRiskValue`.
  - **Delete is blocked** when the item is used by a non-archived menu item → **409 `ITEM_IN_USE`** with the referencing dishes (ADR 0001); otherwise it deletes.
  - There is **no `SKU` field** ("SKUs" = item count); add/edit forms include **par level** and a **category picker** (fixed list, not free-text).

## 5. Menu Builder / AI Recommendations

**Route:** `/menu` · **Auth:** required

- **Displayed data:**
  - **AI-suggested specials:** cards (dish name, ingredients used, "uses expiring items" badge, short explanation) based on expiring inventory.
  - **Regular menu:** dish cards (name, ingredients). *(Dish `% margin` and menu pricing shown in mockups are **stretch** — no price field in the MVP contract.)*
  - Section filters (Starters / Pasta / Mains) + an AI-Specials filter. *(Course sections need a `section` field on `MenuItem` — see open items.)*
- **User actions:** **Generate** recommendations; **Add to tonight** (accept → adds an ACTIVE menu item); **dismiss**; **save for later**; **add/edit** a dish (name, description, ingredients, `isSpecial`); view recipe detail (ingredients + live availability).
- **API needs:** `POST /api/recommendations/generate` · `GET /api/recommendations` · `PATCH /api/recommendations/:id` (status: accept/dismiss/save) · menu items `POST` / `PATCH` (archive via status) · `GET /api/menu/availability`.
- **DB needs:** `MenuItem` (name, description?, isSpecial, status, ingredients[]), `MenuItemIngredient` (inventoryItemId, name, quantity, unit), `Recommendation` (name, explanation, status, `ingredientsUsed[]` snapshot, createdAt).
- **Rules:**
  - Recommendation status ∈ PROPOSED/ACCEPTED/DISMISSED/SAVED; **accept** creates an ACTIVE menu item.
  - Availability per canonical `isAvailable` / `limitingIngredient` (unit-normalized), computed live.
  - **Fallback:** if the AI is unavailable/returns bad output, a rule-based recommendation is returned, flagged as fallback (ADR 0005). App must not crash.
  - Explanation rendered as safe plain text. Menu items **soft-archive**, no hard delete (ADR 0002).

## 6. Suppliers

**Route:** `/suppliers` · **Auth:** required

- **Displayed data (MVP):** supplier directory — name, contact name, email/phone, delivery cadence; search/filter by name; status.
- **User actions (MVP):** view list; **add** supplier; **edit** supplier. **No delete** in MVP (ADR 0003).
- **API needs:** `GET /api/suppliers` · `GET /api/suppliers/:id` · `POST /api/suppliers` · `PATCH /api/suppliers/:id`.
- **DB needs:** `Supplier` (name, contactName?, email?, phone?, deliveryCadence?); referenced by InventoryItem (nullable).
- **Rules:** validate supplier fields; suppliers link to inventory items; loading/empty/error states.
- **Out of MVP (the mockup shows the full vision):** recent **order history** (stretch — T-9S), **spend-by-supplier** analytics, and **upcoming deliveries** (out-of-scope). MVP = directory + add/edit only.

## 7. Reports

**Route:** `/reports` · **Auth:** required

- **Displayed data:**
  - **KPI cards:** Total inventory value · At-risk value · Items below par.
  - **Inventory by category** table (category, item count, total/at-risk value, status).
  - **AI recommendation history** (dish, status accepted/dismissed/saved, date).
  - **Waste-risk / expiry breakdown** (items at risk with expiry + value at risk).
- **User actions:** view; sort the category table; export *(if time allows)*. Date-range filtering is **stretch** (T-10S).
- **API needs:** `GET /api/reports/kpis` · `GET /api/reports/category` · `GET /api/reports/recommendationHistory` (read-only). *(Endpoint names inferred from the cache-invalidation matrix — confirm exact paths with the contract / T-13.)*
- **DB needs:** reads InventoryItem, Recommendation, MenuItem.
- **Rules:** summary totals computed server-side; recommendation history reflects current status; loading/empty/error states. **Advanced analytics** (waste trends, **dish margins**, turnover, time-range, AI-impact metrics) are **out of MVP** (stretch).

---

## Cross-cutting requirements (all pages)

- **Auth:** every page except Login requires a valid Firebase ID token (`Authorization: Bearer`); 401 → redirect to Login. No per-role gating in MVP.
- **Navigation:** a persistent top nav (Today, Inventory, Menu, Suppliers, Reports) + a user menu appears on all authenticated pages.
- **Sign-out:** available from the user menu on every authenticated page; clears the session and redirects to Login.
- **Profile:** captured once at onboarding; there is no profile-edit screen in MVP (`PATCH /api/users` is out of scope).
- **States:** every data view implements loading / empty / error (with retry) / success.
- **Derived fields:** never computed on the client — always from the API (ADR 0004).
- **Writes refresh reads** per the cache-invalidation matrix (e.g., an inventory write refreshes dashboard, reports, and menu availability).

## Acceptance criteria (T-2)

- [ ] All five MVP pages (+ Login, Onboarding) documented with displayed data, user actions, API needs, DB needs, and rules
- [ ] API dependencies noted per page (specific endpoints)
- [ ] MVP vs stretch/out-of-scope clearly marked
- [ ] Locked decisions captured (categories enum, no RBAC, margin stretch); open item (expiring window) flagged for Product Owner
- [ ] Reviewed and approved by the team before development begins
