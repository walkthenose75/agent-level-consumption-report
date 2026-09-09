# Agent-Level Consumption Report

This Power Platform solution contains a manually triggered Power Automate cloud
flow that retrieves the latest 30 days of tenant-level `MCSMessages` resource
consumption and writes a timestamped CSV file to OneDrive for Business.

## Customer installation

Import the managed solution:

`artifacts/AgentLevelConsumptionReport_1.0.0.1_managed.zip`

During import, map these connection references:

1. **Agent Consumption - Power Platform API**
   - Connector: HTTP with Microsoft Entra ID (preauthorized)
   - Base Resource URL: `https://api.powerplatform.com`
   - Microsoft Entra ID Resource URI: `https://api.powerplatform.com`
2. **Agent Consumption - OneDrive for Business**
   - Select a OneDrive connection owned by the intended flow owner.

The HTTP connection owner should be a member account in the customer's tenant
with the Power Platform Administrator role. The flow also requires licensing
that permits the premium HTTP connector, and applicable DLP policies must allow
both connectors.

After import, verify both connection references, turn on **Agent-Level
Consumption Report**, and run it manually. The CSV is created in the root of
the mapped OneDrive account with a name such as:

`PPAC_Agent_Level_Consumption_Report_20260908_223000.csv`

## Behavior and limitations

- The tenant must have active prepaid or pay-as-you-go `MCSMessages` entitlement.
- If no entitlement is available, the run terminates with
  `NoCopilotCreditsEntitlement`.
- The report uses the supported Power Platform Entitlement Insight API,
  version `2024-10-01`.
- The current flow requests up to 5,000 resource records for the reporting
  period. Tenants exceeding that result size require continuation-token
  pagination before this package should be used for complete reporting.
- Connections and credentials are not included in either solution ZIP.

## Repository contents

- `src/` - unpacked unmanaged solution source for development and review.
- `artifacts/*_managed.zip` - customer deployment artifact.
- `artifacts/*_unmanaged.zip` - development/import artifact.
- `deployment-settings.example.json` - connection-reference mapping template;
  populate `ConnectionId` values only in a local copy.
