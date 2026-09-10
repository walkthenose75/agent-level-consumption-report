# Agent-Level Consumption Report

A Power Platform solution containing a manually triggered Power Automate cloud
flow that retrieves the latest 30 days of tenant-level `MCSMessages`
consumption and writes a timestamped CSV file to OneDrive for Business.

## Customer installation

Import the unmanaged solution from:

[`artifacts/AgentLevelConsumptionReport_1.0.0.1_unmanaged.zip`](artifacts/AgentLevelConsumptionReport_1.0.0.1_unmanaged.zip)

This repository distributes only the unmanaged package. Components imported
from it can be edited in the destination environment and are not removed
automatically when the solution container is deleted.

Expected SHA-256 for the unmanaged ZIP:

```text
092304C24392461E885E7CB56EA80E6BC2148759C50B48D0ED9402A182918FE9
```

Verify it in PowerShell:

```powershell
Get-FileHash .\artifacts\AgentLevelConsumptionReport_1.0.0.1_unmanaged.zip -Algorithm SHA256
```

## Prerequisites

- A Power Platform environment where you can import solutions.
- A member account in the customer tenant with the Power Platform
  Administrator role for the API connection.
- Licensing that permits the premium HTTP connector.
- DLP policies that allow the HTTP with Microsoft Entra ID and OneDrive for
  Business connectors.
- Active prepaid or pay-as-you-go `MCSMessages` entitlement.

## Import and configure

1. In Power Apps, open **Solutions** and select **Import solution**.
2. Upload the unmanaged ZIP from `artifacts/`.
3. Map **Agent Consumption - Power Platform API**
   (`crc57_AgentConsumptionPowerPlatformApi`) to an HTTP with Microsoft Entra
   ID connection configured with both values below:
   - Base Resource URL: `https://api.powerplatform.com`
   - Microsoft Entra ID Resource URI: `https://api.powerplatform.com`
4. Map **Agent Consumption - OneDrive for Business**
   (`crc57_AgentConsumptionOneDrive`) to the intended flow owner's OneDrive
   connection.
5. Complete the import and confirm solution version `1.0.0.1` is installed.
6. Open **Agent-Level Consumption Report**, review its connections and output
   location, and then turn it on.
7. Run the flow manually and confirm a CSV is created in the mapped OneDrive
   root with a name such as
   `PPAC_Agent_Level_Consumption_Report_20260908_223000.csv`.

Connections and credentials are not included in the solution ZIP.

## Report behavior

The flow uses Power Platform Entitlement Insight API version `2024-10-01` to:

1. Confirm that the tenant has an `MCSMessages` entitlement.
2. Retrieve the latest 30 days of resource consumption.
3. Select agent, environment, usage, product, feature, channel, and unit data.
4. Write the selected data to CSV in OneDrive.

If no entitlement is available, the run terminates with
`NoCopilotCreditsEntitlement`.

The current flow requests at most 5,000 resource records. If the CSV contains
exactly 5,000 data rows, treat it as potentially truncated rather than a
complete report. Larger result sets require continuation-token pagination
validated against the current API contract.

## Repository layout

| Path                               | Purpose                                                       |
| ---------------------------------- | ------------------------------------------------------------- |
| `artifacts/*_unmanaged.zip`        | Importable unmanaged solution package                         |
| `src/`                             | Unpacked unmanaged Power Platform solution source             |
| `deployment-settings.example.json` | Connection mapping template; keep actual connection IDs local |
| `solution-manifest.json`           | Release identity, artifact paths, and SHA-256 checksums       |

## Development

Edit the flow in a development environment, export the unmanaged package, and
unpack it into `src/`. Keep the version in `solution-manifest.json`
synchronized with `src/Other/Solution.xml`, and update the artifact checksum
for every release.

Do not commit credentials, access tokens, connection IDs, or environment URLs.
