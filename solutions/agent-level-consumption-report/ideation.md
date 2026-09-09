# agent-level-consumption-report - project ideation

> Complete this document before provisioning. It is the shared source of truth
> for what the solution is and why. Guided mode will remind you to fill it in.

## Problem statement

Automate generation and storage of the Microsoft 365 agent-level entitlement consumption report shown in the supplied reference screenshot.

## Users and personas

- Primary user: Microsoft 365 licensing or Copilot adoption analyst.
- Secondary users: Power Platform and Microsoft 365 operations teams.
- Administrators: Power Platform environment administrators and connection owners.

## Desired outcomes and success metrics

- Outcome: Generate the latest 30-day per-agent entitlement consumption report without manually calling the licensing export API.
- How we will measure success: A timestamped CSV is created in OneDrive after each successful run and contains the downloaded report payload.

## In-scope workloads

- [ ] Dataverse schema (tables, columns, relationships)
- [ ] Sample or reference data
- [ ] Code App
- [ ] Model-driven app
- [x] Power Automate flows
- [ ] Power Pages
- [ ] PCF control
- [ ] Security roles

## Data model sketch

| Table | Purpose | Key columns | Relationships |
|---|---|---|---|
| None | This iteration stores the generated CSV in OneDrive and does not require Dataverse. | N/A | N/A |

## Automation

Create a manually triggered cloud flow based on the supplied screenshot:

1. Send a POST request through the Microsoft 365 licensing connector to
   `licensing/Downloads?api-version=1`.
2. Request `EntitlementConsumptionTenantPerAgentDetailsReport` for the
   `MCSMessages` entitlement with a 30-day lookback.
3. Parse the response to obtain the asynchronous download `id`.
4. Wait one minute for report generation.
5. Download the report from
   `licensing/Downloads/download/{id}?api-version=1`.
6. Save the response body to OneDrive as a timestamped CSV named
   `PPAC_Agent_Level_Consumption_Report_<timestamp>.csv`.

The flow will be created stopped so connections and output location can be
reviewed before it is enabled.

## Security roles

No Dataverse security roles are required. The flow owner needs permission to
invoke the Microsoft 365 licensing endpoint and create files through the
selected OneDrive for Business connection.

## Out of scope

- Scheduled recurrence or automatic publishing.
- Dataverse storage, dashboards, notifications, and downstream analytics.
- Additional entitlement, application, SKU, or license classification filters.

## Risks and open questions

- The report generation API may occasionally require more than one minute; the
  initial implementation follows the screenshot and can be changed to polling
  if runtime evidence shows the fixed delay is unreliable.
- FlowAgent must have access to the selected development environment and valid
  Microsoft 365 licensing and OneDrive for Business connections.

## Provisioning plan

The executable recipe lives in `solution.plan.json` next to this document.
Run `npm run solution:plan` to review the dependency-ordered steps.
