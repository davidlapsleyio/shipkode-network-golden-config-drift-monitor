# Automated Fleet-Wide Compliance Auditing with Dynamic Masking

As a Scale-Oriented Architect, I want to execute automated, masked configuration audits across the 4,000-device fleet so that I can eliminate 95% of false-positive alerts and provide 100% visibility to the CISO.

## Actors

- Scale-Oriented Architect
- Management API

## Preconditions

- Management API credentials are valid and have read-access.
- Golden Configuration templates are mapped to device groups.

## Main Flow

1. The Architect defines a Golden Configuration template with regex-based masking for timestamps and dynamic variables.
2. The Architect schedules a fleet-wide audit across 4,000 devices via the tool's CLI.
3. The tool authenticates with the Management API to pull current 'running-configs'.
4. The tool applies masking rules to neutralize non-functional differences.
5. The tool generates a delta report highlighting only unauthorized structural or value changes.

## Postconditions

- A standardized compliance report is available for CISO review.
- A timestamped snapshot of all device drifts is stored in the historical database.

## Acceptance Criteria

- Drift report is generated in < 30 seconds for 4,000 devices.
- 0 false positives reported from system-generated timestamps or dynamic Uptime counters.
- Final report includes a 'Drift Percentage' metric for the entire fleet.

