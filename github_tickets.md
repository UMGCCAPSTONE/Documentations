# GitHub Project Board Tickets

---

## SPRINT 1: Scope, Setup, and DevOps Bootstrap

---

### T-1: Finalize MVP Scope

**Title:** `T-1: Finalize MVP Scope`

**Description:**
Define and lock the MVP feature set for the AI Inventory Management application. Identify which features are required for a functional demo submission versus stretch goals. Produce a short scope document or shared reference that the team can use throughout the project to make prioritization decisions.

**Acceptance Criteria:**
- MVP features are clearly listed and agreed upon by the team
- Stretch goals are explicitly labeled and separated from required work
- Document is accessible to all team members (wiki, README section, or pinned comment)

**Sprint:** Sprint 1
**Labels:** `scope` `planning`

---

### T-2: Define Page-Level Requirements

**Title:** `T-2: Define Page-Level Requirements`

**Description:**
Document the requirements for each page/view in the MVP application: Dashboard, Inventory, Menu Builder / AI Recommendations, Suppliers, and Reports. For each page, define the key data displayed, the primary user actions, and any API dependencies.

**Acceptance Criteria:**
- All MVP pages are documented with their required data fields and interactions
- API dependencies are noted per page
- Requirements are reviewed and approved by the team before development begins

**Sprint:** Sprint 1
**Labels:** `scope` `planning`

---

### T-3: Create User Stories and Acceptance Criteria

**Title:** `T-3: Create User Stories and Acceptance Criteria`

**Description:**
Write user stories for all MVP features using the standard format ("As a [role], I want to [action] so that [benefit]"). Attach acceptance criteria to each story. These stories will serve as the basis for ticket scoping and testing validation.

**Acceptance Criteria:**
- User stories written for all MVP pages and core interactions
- Each story has at least one acceptance criterion
- Stories are added to the project board or linked from the milestone

**Sprint:** Sprint 1
**Labels:** `scope` `planning`

---

### T-4: Frontend Setup (React/Vite, Routing, Components, API Config)

**Title:** `T-4: Frontend Setup`

**Description:**
Bootstrap the frontend application. Set up the React + Vite project structure, configure client-side routing, establish a shared component library scaffold, and configure the API client (base URL, auth headers, interceptors). Ensure the dev server runs locally and is ready for feature development.

**Acceptance Criteria:**
- React + Vite project initializes and runs without errors
- Routing is configured for all MVP pages (stubs acceptable)
- Shared component scaffold is in place (layout, nav, common UI elements)
- API client is configured with base URL and placeholder auth header support
- Project structure follows agreed conventions and is documented

**Sprint:** Sprint 1
**Labels:** `frontend` `setup`

---

### T-11: Database Schema

**Title:** `T-11: Database Schema`

**Description:**
Design and implement the database schema for all MVP entities: users, inventory items, suppliers, menu recommendations, and the recommendations log. Write and apply migrations. Verify that the schema supports all required API operations.

**Acceptance Criteria:**
- Tables created for: `users`, `inventory`, `suppliers`, `menu_recommendations`, `recommendation_history`
- All foreign key relationships are defined and enforced
- Migrations are scripted and repeatable
- Schema is documented (ERD or table definitions in README/wiki)

**Sprint:** Sprint 1
**Labels:** `backend` `database` `setup`

---

### T-25: Generate LLM API Key

**Title:** `T-25: Generate LLM API Key`

**Description:**
Generate and securely store the API key for the LLM provider being used for AI recommendations. Confirm the key works with a basic test request. Document how the key should be referenced in environment configuration (without exposing the value).

**Acceptance Criteria:**
- LLM API key is generated and confirmed working
- Key is stored in the team's secure secrets location (not committed to the repo)
- `.env.example` is updated with the expected variable name
- Basic connectivity test passes

**Sprint:** Sprint 1
**Labels:** `devops` `ai` `setup`

---

### T-26: Secure Local Environments

**Title:** `T-26: Secure Local Environments`

**Description:**
Establish a consistent, secure local development environment for all team members. Set up `.env` file conventions, ensure no secrets are committed, and configure `.gitignore` accordingly. Document the local setup steps in the README.

**Acceptance Criteria:**
- `.gitignore` covers all `.env` files and secrets
- `.env.example` lists all required environment variables with placeholder values
- Local setup instructions are added to the README
- All team members can run the project locally using the documented steps

**Sprint:** Sprint 1
**Labels:** `devops` `setup`

---

### T-22: Provision and Configure EC2 Server

**Title:** `T-22: Provision and Configure EC2 Server`

**Description:**
Provision the AWS EC2 instance that will host the demo environment. Install required dependencies (Docker, Docker Compose, etc.), configure security groups and firewall rules, and verify SSH access for the team.

**Acceptance Criteria:**
- EC2 instance is running and accessible via SSH
- Security groups allow only necessary inbound traffic (HTTP/HTTPS + SSH)
- Docker and Docker Compose are installed and verified
- Instance details are documented for the team

**Sprint:** Sprint 1
**Labels:** `devops` `infrastructure`

---

### T-19: Setup GitHub Actions Workflow

**Title:** `T-19: Setup GitHub Actions Workflow`

**Description:**
Create the initial GitHub Actions CI workflow. At minimum, the workflow should trigger on pull requests and run linting and any existing tests. The pipeline should be structured to support deployment steps in a later ticket.

**Acceptance Criteria:**
- GitHub Actions workflow file is committed to the repo
- Workflow triggers on PRs to `main` (or agreed branch)
- Linting step passes on a clean codebase
- Pipeline structure supports future deployment steps

**Sprint:** Sprint 1
**Labels:** `devops` `ci-cd`

---

## SPRINT 2: Backend, Frontend, and AI Development

---

### T-12: Inventory API (CRUD)

**Title:** `T-12: Inventory API (CRUD)`

**Description:**
Build the full CRUD REST API for inventory items. Endpoints must support listing all items (with filtering/sorting), retrieving a single item, creating, updating, and deleting items. Responses should include all fields required by the Inventory UI.

**Acceptance Criteria:**
- `GET /inventory` — returns paginated/filterable list of items
- `GET /inventory/:id` — returns a single item
- `POST /inventory` — creates a new item with validation
- `PUT /inventory/:id` — updates an item with validation
- `DELETE /inventory/:id` — deletes an item
- All endpoints return appropriate HTTP status codes and error messages
- API is tested with at least basic happy-path and error-case coverage

**Sprint:** Sprint 2
**Labels:** `backend` `api`

---

### T-14: Supplier API

**Title:** `T-14: Supplier API`

**Description:**
Build the REST API for supplier management. Endpoints must support listing suppliers, retrieving a single supplier, creating, and updating supplier records. Responses should include all fields required by the Supplier UI.

**Acceptance Criteria:**
- `GET /suppliers` — returns list of suppliers
- `GET /suppliers/:id` — returns a single supplier
- `POST /suppliers` — creates a new supplier with validation
- `PUT /suppliers/:id` — updates a supplier with validation
- All endpoints return appropriate HTTP status codes and error messages

**Sprint:** Sprint 2
**Labels:** `backend` `api`

---

### T-13: Dashboard API

**Title:** `T-13: Dashboard API`

**Description:**
Build the API endpoints that power the Dashboard. This includes summary metrics (total inventory count, low-stock count, expiring item count, at-risk value), the urgent alerts feed (low-stock and expiring items), and the AI recommendation preview data.

**Acceptance Criteria:**
- `GET /dashboard/summary` — returns KPI metrics
- `GET /dashboard/alerts` — returns low-stock and expiring item alerts
- `GET /dashboard/recommendations/preview` — returns preview of AI specials
- All fields required by the Dashboard UI are present in responses
- Endpoints handle empty states gracefully

**Sprint:** Sprint 2
**Labels:** `backend` `api`

---

### T-16: AI Prompt Workflow

**Title:** `T-16: AI Prompt Workflow`

**Description:**
Implement the core AI prompt workflow that generates menu recommendations from current inventory data. Design and test the prompt template that sends relevant inventory context (low-stock items, expiring items, available ingredients) to the LLM and parses the structured response.

**Acceptance Criteria:**
- Prompt template is defined and version-controlled
- Inventory context is correctly selected and formatted before sending to the LLM
- LLM response is parsed into a structured format (name, ingredients used, explanation)
- Prompt and parsing logic are unit-testable in isolation
- Handles LLM API errors gracefully

**Sprint:** Sprint 2
**Labels:** `backend` `ai`

---

### T-15: Menu / Recommendation API

**Title:** `T-15: Menu / Recommendation API`

**Description:**
Build the API endpoints for the Menu Builder / AI Recommendations feature. This includes triggering a new recommendation generation, retrieving the current recommendations, and recording accept/dismiss/save actions on individual recommendations.

**Acceptance Criteria:**
- `POST /recommendations/generate` — triggers AI recommendation generation and returns results
- `GET /recommendations` — returns current/saved recommendations
- `POST /recommendations/:id/accept` — marks a recommendation as accepted
- `POST /recommendations/:id/dismiss` — marks a recommendation as dismissed
- Depends on T-16 (AI Prompt Workflow) being complete
- All endpoints return appropriate status codes and error messages

**Sprint:** Sprint 2
**Labels:** `backend` `api` `ai`

---

### T-17: Fallback Logic

**Title:** `T-17: Fallback Logic`

**Description:**
Implement fallback behavior for the AI recommendation workflow for cases where the LLM API is unavailable, returns an error, or produces an unparseable response. The fallback should ensure the application degrades gracefully without crashing or exposing raw errors to the user.

**Acceptance Criteria:**
- LLM timeout and API error cases are caught and handled
- Fallback response is returned (e.g., a default message or cached result) when the LLM is unavailable
- Malformed LLM responses do not cause unhandled exceptions
- Fallback behavior is documented and testable

**Sprint:** Sprint 2
**Labels:** `backend` `ai`

---

### T-5: Authentication UI

**Title:** `T-5: Authentication UI`

**Description:**
Build the login and authentication screens. Implement the login form with email/password fields, form validation, error state display, and integration with the auth API. Include session/token handling so authenticated state persists across page navigation.

**Acceptance Criteria:**
- Login page renders with email and password fields
- Form validates required fields and shows inline errors
- Successful login stores the auth token and redirects to the Dashboard
- Failed login displays an appropriate error message
- Authenticated routes redirect unauthenticated users to the login page
- Logout clears session/token

**Sprint:** Sprint 2
**Labels:** `frontend` `auth`

---

### T-7A: Inventory Page Metrics + Layout

**Title:** `T-7A: Inventory Page — Metrics & Layout`

**Description:**
Build the Inventory page shell and top-of-page metric cards. Cards should display: total items, total SKUs, items expiring soon, at-risk value, and low-stock count. Connect metric cards to the Inventory API.

**Acceptance Criteria:**
- Inventory page renders with the correct layout
- Metric cards display all required KPIs
- Data is fetched from the API on page load
- Loading and error states are handled for the metrics section

**Sprint:** Sprint 2
**Labels:** `frontend` `inventory`

---

### T-7B: Inventory DataGrid Component

**Title:** `T-7B: Inventory DataGrid Component`

**Description:**
Build the main inventory data table. The grid should display all inventory item fields, support search, column filtering, and sorting. Implement loading, empty, and error states. Connect to `GET /inventory`.

**Acceptance Criteria:**
- Table renders all required item fields (name, SKU, category, quantity, unit, expiry date, cost, etc.)
- Search input filters results in real time (or on submit)
- Column sorting works for at least key fields
- Filters work for category and/or status
- Loading skeleton or spinner displays while data is fetching
- Empty state is shown when no items match
- Error state is shown on API failure

**Sprint:** Sprint 2
**Labels:** `frontend` `inventory`

---

### T-7C: Inventory Item Management Flow

**Title:** `T-7C: Inventory Item Management — Add / Edit / Delete`

**Description:**
Build the add, edit, and delete item flows. Implement a form/modal for creating and editing items with full validation and success/error feedback. Implement delete confirmation and connect all actions to the corresponding API endpoints.

**Acceptance Criteria:**
- "Add Item" opens a form/modal with all required fields
- All fields are validated before submission
- Successful create adds the item to the table without a full page reload
- Edit flow pre-populates the form with existing item data
- Successful update reflects immediately in the table
- Delete shows a confirmation prompt and removes the item on confirm
- API errors are displayed to the user with a helpful message

**Sprint:** Sprint 2
**Labels:** `frontend` `inventory`

---

### T-7S: CSV Import/Export (Stretch)

**Title:** `T-7S: CSV Import/Export — STRETCH`

**Description:**
Add CSV import and export functionality to the Inventory page. Export should generate a downloadable CSV of current inventory. Import should allow users to upload a CSV to bulk-add or update items.

> ⚠️ **Stretch goal** — only implement if core MVP tickets are complete.

**Acceptance Criteria:**
- Export button downloads a CSV of all current inventory items
- Import accepts a CSV file in the expected format and creates/updates items
- Import validation shows errors for malformed rows without aborting the entire import

**Sprint:** Sprint 2
**Labels:** `frontend` `inventory` `stretch`

---

### T-9A: Supplier Directory Component

**Title:** `T-9A: Supplier Directory Component`

**Description:**
Build the Supplier page directory view/table. Display supplier records with key fields (name, contact, category, etc.). Connect to the Supplier API and implement loading, empty, and error states.

**Acceptance Criteria:**
- Supplier table renders all required fields
- Data is fetched from `GET /suppliers` on page load
- Loading, empty, and error states are all handled
- Basic search or filter is present (name or category)

**Sprint:** Sprint 2
**Labels:** `frontend` `suppliers`

---

### T-9B: Supplier Add/Edit Flow

**Title:** `T-9B: Supplier Add / Edit Flow`

**Description:**
Build the add and edit supplier flows. Implement a form/modal with validation, success/error feedback, and API integration for creating and updating supplier records.

**Acceptance Criteria:**
- "Add Supplier" opens a form with all required fields
- All required fields are validated before submission
- Successful create adds the supplier to the directory
- Edit pre-populates the form with existing supplier data
- Successful update reflects in the directory
- API errors are surfaced to the user

**Sprint:** Sprint 2
**Labels:** `frontend` `suppliers`

---

### T-9S: Supplier Order History / Upcoming Deliveries (Stretch)

**Title:** `T-9S: Supplier Order History & Upcoming Deliveries — STRETCH`

**Description:**
Add supplier order history, spend analytics, or upcoming delivery tracking to the Supplier detail view.

> ⚠️ **Stretch goal** — only implement if core MVP tickets are complete.

**Acceptance Criteria:**
- Supplier detail view shows order history or upcoming deliveries
- Data is fetched from a supporting API endpoint
- Section handles empty and error states

**Sprint:** Sprint 2
**Labels:** `frontend` `suppliers` `stretch`

---

### T-6A: Dashboard Summary Cards + Metrics

**Title:** `T-6A: Dashboard Summary Cards & Metrics`

**Description:**
Build the Dashboard summary metric cards. Cards should display: total inventory value, low-stock item count, expiring item count, and at-risk value. Include any basic chart retained for the MVP. Connect all cards to the Dashboard API with loading and error states.

**Acceptance Criteria:**
- All required KPI cards render on the Dashboard
- Data is fetched from the Dashboard summary API on page load
- Loading state displays while data is fetching
- Error state is shown if the API call fails
- Any MVP-retained chart displays correctly

**Sprint:** Sprint 2
**Labels:** `frontend` `dashboard`

---

### T-6B: Urgent Alerts Section

**Title:** `T-6B: Dashboard Urgent Alerts Section`

**Description:**
Build the urgent inventory alerts section on the Dashboard. Display low-stock and expiring item alerts pulled from the Dashboard alerts API. Implement appropriate loading and empty states.

**Acceptance Criteria:**
- Alerts section renders below or alongside the summary cards
- Low-stock and expiring item alerts are displayed with relevant item info
- Data is fetched from the alerts API on page load
- Loading and empty states are handled
- Alerts link to or highlight the relevant inventory item where possible

**Sprint:** Sprint 2
**Labels:** `frontend` `dashboard`

---

### T-6C: AI Recommendation Preview Cards

**Title:** `T-6C: Dashboard AI Recommendation Preview Cards`

**Description:**
Build the AI recommendation preview section on the Dashboard. Display a short preview of current AI-generated specials/recommendations. Include loading and error states, and basic interaction behavior (e.g., navigate to full recommendation view).

**Acceptance Criteria:**
- AI recommendation preview cards render on the Dashboard
- Data is fetched from the recommendations preview API
- Loading and error states are handled
- Clicking a card or a CTA navigates to the full Menu Builder / AI Recommendations page

**Sprint:** Sprint 2
**Labels:** `frontend` `dashboard` `ai`

---

### T-8A: Menu Recommendation Page + Generate Flow

**Title:** `T-8A: Menu Builder — Page Layout & Generate Flow`

**Description:**
Build the Menu Builder / AI Recommendations page layout and the primary "Generate Recommendations" action. Connect the generate button to the recommendation API, display a loading state during generation, and handle errors.

**Acceptance Criteria:**
- Page renders with the correct layout and heading
- "Generate Recommendations" button triggers `POST /recommendations/generate`
- Loading state (spinner or skeleton) displays while the AI generates results
- Error state is shown if the API call fails
- Successful generation transitions to displaying the recommendation cards (T-8B)

**Sprint:** Sprint 2
**Labels:** `frontend` `menu` `ai`

---

### T-8B: Recommendation Cards, Explanation, and Actions

**Title:** `T-8B: Recommendation Cards — Detail, Explanation & Actions`

**Description:**
Build the individual recommendation cards that display each AI-generated special. Each card should show the dish name, ingredients used, a short explanation, and action buttons (accept, dismiss, save). Include a recipe/detail view and connect all actions to the API with success/error feedback.

**Acceptance Criteria:**
- Recommendation cards display: name, ingredients used, short explanation
- "View Recipe / Detail" expands or navigates to a detail view
- Accept, dismiss, and save actions call the appropriate API endpoints
- Success feedback is shown after each action (toast, status update, etc.)
- Error feedback is shown if an action fails
- Dismissed cards are removed from or de-emphasized in the list

**Sprint:** Sprint 2
**Labels:** `frontend` `menu` `ai`

---

### T-8S: Advanced Menu Management (Stretch)

**Title:** `T-8S: Advanced Menu Management — STRETCH`

**Description:**
Add menu management features beyond the core recommendation cards, such as a saved menu list, editing saved specials, or historical recommendation browsing.

> ⚠️ **Stretch goal** — only implement if core MVP tickets are complete.

**Acceptance Criteria:**
- Saved specials are viewable in a menu list
- Users can edit or remove saved menu items
- Historical recommendations are browsable

**Sprint:** Sprint 2
**Labels:** `frontend` `menu` `stretch`

---

### T-10A: Reports Page — Basic KPI Cards

**Title:** `T-10A: Reports Page — Basic KPI Cards`

**Description:**
Build the Reports page and the top-level KPI summary cards. Cards should display: total inventory value, low-stock item totals, expiring item counts, and waste-risk summary. Connect to the Reports API.

**Acceptance Criteria:**
- Reports page renders with the correct layout
- KPI cards display all required metrics
- Data is fetched from the API on page load
- Loading and error states are handled

**Sprint:** Sprint 2
**Labels:** `frontend` `reports`

---

### T-10B: Reports — Category Summary Table

**Title:** `T-10B: Reports — Category Summary Table`

**Description:**
Build the category summary table on the Reports page. Display inventory data broken down by category using report-ready API data. Implement loading, empty, and error states.

**Acceptance Criteria:**
- Category summary table renders with correct columns
- Data is fetched from the reports API
- Loading, empty, and error states are all handled
- Table is readable and sortable by at least one column

**Sprint:** Sprint 2
**Labels:** `frontend` `reports`

---

### T-10C: Reports — Recommendation History & Waste-Risk Summary

**Title:** `T-10C: Reports — Recommendation History & Waste-Risk Summary`

**Description:**
Build the recommendation history section and the waste-risk summary section on the Reports page. Pull data from the API and display in a readable format.

**Acceptance Criteria:**
- Recommendation history section shows past AI recommendations with status (accepted/dismissed/saved)
- Waste-risk summary displays items at risk of expiring or going to waste
- Both sections handle loading, empty, and error states
- Data matches what is stored via the recommendation action API

**Sprint:** Sprint 2
**Labels:** `frontend` `reports`

---

### T-10S: Advanced Analytics (Stretch)

**Title:** `T-10S: Advanced Analytics — STRETCH`

**Description:**
Add advanced analytics to the Reports page: dish margin rankings, inventory turnover metrics, historical comparisons, time-range filtering, or advanced chart customization.

> ⚠️ **Stretch goal** — only implement if core MVP tickets are complete.

**Acceptance Criteria:**
- At least one advanced analytics section is added (margin rankings, turnover, or historical comparison)
- Time-range filter works and updates all relevant charts/tables
- Charts are readable and correctly labeled

**Sprint:** Sprint 2
**Labels:** `frontend` `reports` `stretch`

---

### T-24: Connect CI/CD to EC2

**Title:** `T-24: Connect CI/CD to EC2`

**Description:**
Configure the GitHub Actions workflow to deploy to the provisioned EC2 instance. Set up SSH-based deployment (or equivalent) so that a successful pipeline run can push updated code to the server.

**Acceptance Criteria:**
- GitHub Actions can SSH into the EC2 instance using a stored secret key
- A deploy step is added to the workflow that runs on merge to `main`
- Deployment does not require manual SSH intervention

**Sprint:** Sprint 2
**Labels:** `devops` `ci-cd`

---

### T-27: Configure Production and Pipeline Secrets

**Title:** `T-27: Configure Production & Pipeline Secrets`

**Description:**
Add all required secrets to the GitHub Actions environment and the EC2 production environment. This includes database credentials, LLM API key, JWT secret, and any other environment-specific values.

**Acceptance Criteria:**
- All required secrets are added to GitHub Actions secrets
- Production `.env` on EC2 is configured with correct values
- No secrets are committed to the repository
- Pipeline runs successfully with secrets injected

**Sprint:** Sprint 2
**Labels:** `devops` `ci-cd`

---

### T-23: Orchestrate Containers (docker-compose)

**Title:** `T-23: Orchestrate Containers (docker-compose)`

**Description:**
Write and test a `docker-compose.yml` that orchestrates the backend API, frontend (or static build), and database containers. Ensure the application runs correctly as a composed stack locally and on EC2.

**Acceptance Criteria:**
- `docker-compose up` starts all services without errors
- Backend API, database, and frontend are all reachable
- Environment variables are injected via `.env` file
- Compose file works on both local and EC2 environments

**Sprint:** Sprint 2
**Labels:** `devops` `infrastructure`

---

### T-20: Integrate Automated Testing (Vitest + Jest)

**Title:** `T-20: Integrate Automated Testing (Vitest + Jest)`

**Description:**
Integrate Vitest (frontend) and Jest (backend) into the project. Configure test runners so that `npm test` runs all tests. Add the test step to the GitHub Actions workflow so tests run on every PR.

**Acceptance Criteria:**
- Vitest is configured and runs frontend unit tests
- Jest is configured and runs backend unit tests
- Both test suites run via a single `npm test` command (or equivalent)
- GitHub Actions workflow runs tests on every PR
- At least one passing smoke test per suite confirms the setup works

**Sprint:** Sprint 2
**Labels:** `devops` `testing`

---

## SPRINT 3: Integration, Testing, Deployment, and Docs

---

### T-18: Final API Integration Review

**Title:** `T-18: Final API Integration Review`

**Description:**
Connect all frontend pages to their corresponding backend API endpoints. Verify that every page correctly fetches, displays, and mutates data end-to-end. Resolve any contract mismatches between frontend expectations and backend responses.

**Acceptance Criteria:**
- Dashboard, Inventory, Menu Builder, Suppliers, and Reports pages all load real data from the backend
- All create/update/delete actions persist to the database
- No pages are using hardcoded mock data in the final build
- API errors surface correctly in the UI across all pages

**Sprint:** Sprint 3
**Labels:** `frontend` `backend` `integration`

---

### T-21: Automate Deployment via Docker Compose

**Title:** `T-21: Automate Deployment via Docker Compose`

**Description:**
Finalize the automated deployment process so that merging to `main` triggers the CI/CD pipeline to pull the latest code, rebuild Docker images, and restart the compose stack on EC2 with zero manual steps.

**Acceptance Criteria:**
- Merging to `main` triggers a full automated deployment
- Docker images are rebuilt and containers are restarted on EC2
- Deployment is verified by a health check (curl or equivalent)
- Rollback procedure is documented

**Sprint:** Sprint 3
**Labels:** `devops` `ci-cd`

---

### T-28: Testing

**Title:** `T-28: Testing`

**Description:**
Perform comprehensive testing of the full application: frontend UI flows, backend API endpoints, database operations, and the AI recommendation flow end-to-end. Document test results and resolve any critical bugs before deployment.

**Acceptance Criteria:**
- All MVP user flows are tested manually (auth, inventory CRUD, recommendation generation, reports)
- Backend API endpoints are tested with both happy-path and error cases
- Database read/write operations are verified
- AI recommendation flow completes end-to-end at least once in the demo environment
- Critical bugs are resolved; known non-blocking issues are documented

**Sprint:** Sprint 3
**Labels:** `testing`

---

### T-29A: Deploy Backend to Demo Environment

**Title:** `T-29A: Deploy Backend to Demo Environment`

**Description:**
Deploy the backend API to the EC2 demo environment. Configure all environment variables, confirm database connectivity, and verify that all backend API routes are reachable from the public URL.

**Acceptance Criteria:**
- Backend API is running on EC2 and reachable via the demo URL
- All environment variables are set correctly in production
- Database connection is confirmed and migrations are applied
- Key API routes return expected responses when hit from outside the server

**Sprint:** Sprint 3
**Labels:** `devops` `deployment`

---

### T-29B: Deploy Frontend to Demo Environment

**Title:** `T-29B: Deploy Frontend to Demo Environment`

**Description:**
Deploy the frontend application to the demo environment. Connect it to the deployed backend API, verify all routing and page access, and confirm the core MVP screens load correctly.

**Acceptance Criteria:**
- Frontend is served from the demo URL
- All MVP pages are accessible without routing errors
- Frontend is correctly pointing to the production backend API
- Dashboard, Inventory, Menu Builder, Suppliers, and Reports all load without console errors

**Sprint:** Sprint 3
**Labels:** `devops` `deployment`

---

### T-30: Documentation

**Title:** `T-30: Documentation`

**Description:**
Write and finalize all project documentation required for submission. This includes the README (setup, run, deploy instructions), a user guide for the application, architecture/design notes, and test evidence.

**Acceptance Criteria:**
- README covers: project overview, local setup steps, environment variable reference, and deployment instructions
- User guide describes how to use each MVP feature
- Architecture section documents key technical decisions (stack, AI workflow, DB schema)
- Test evidence is included (screenshots, test run output, or a test summary document)

**Sprint:** Sprint 3
**Labels:** `documentation`

---

### T-31: Demo Prep

**Title:** `T-31: Demo Prep`

**Description:**
Prepare the application and materials for the final demo. Seed realistic demo data, prepare a walkthrough script, capture screenshots of each MVP screen, and set up a backup plan in case of live demo issues.

**Acceptance Criteria:**
- Demo environment is seeded with realistic inventory, supplier, and recommendation data
- Demo walkthrough script covers all MVP features
- Screenshots of all MVP screens are captured and saved
- Backup plan is documented (e.g., recorded video or local fallback)

**Sprint:** Sprint 3
**Labels:** `demo` `documentation`
