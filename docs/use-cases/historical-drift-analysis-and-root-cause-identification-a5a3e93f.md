# Historical Drift Analysis and Root Cause Identification

As an Operational Auditor, I want to quickly identify 'who changed what' and when a drift occurred so that I can decrease time spent on manual audits by 80% and speed up troubleshooting.

## Actors

- Operational Auditor
- Compliance Architect

## Preconditions

- Logging and historical tracking are enabled in the tool.
- The tool is integrated with the regional management API endpoints.

## Main Flow

1. The tool detects a configuration mismatch during its hourly polling cycle.
2. The tool identifies which specific lines in the 2,000-line config blob have changed.
3. The tool logs the drift event along with a side-by-side comparison of 'Expected' vs 'Actual'.
4. The Auditor queries the historical log to identify the exact window when the drift occurred.
5. The Auditor exports the diff as a PDF for the quarterly compliance evidence folder.

## Postconditions

- A permanent record of the drift incident is archived.
- Operational teams are alerted to the specific nature of the violation.

## Acceptance Criteria

- Audit trail includes the exact timestamp of drift detection.
- Delta report shows the specific JSON keys or CLI lines that deviated.
- MTTD (Mean Time to Detect) for configuration errors is reduced to the frequency of the poll interval.

