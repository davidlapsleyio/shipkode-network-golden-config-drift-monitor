# Root-Cause Analysis via Historical Drift Logs

As a Resource-Constrained Operator, I want to access a historical record of when drift occurred so that I can decrease troubleshooting time by 50% for configuration-related outages.

## Actors

- Resource-Constrained Operator
- Scale-Oriented Architect

## Preconditions

- The tool has been running scheduled audits for at least 48 hours.
- Historical configuration snapshots are stored in the local database.

## Main Flow

1. The automated scheduler detects a drift on a core data center switch.
2. The tool logs the drift event and archives the deviating configuration.
3. The Operator accesses the historical drift log for the specific device ID.
4. The Operator compares the current drifted state against the last known compliant state from 24 hours prior.
5. The Operator uses the delta report to identify the unauthorized 'no shut' on an interface.

## Postconditions

- Audit trail updated with the details of the configuration delta.
- The team has documented evidence of when the config drift occurred.

## Acceptance Criteria

- The historical log displays the exact timestamp the drift was first detected.
- The system identifies the specific lines of configuration that changed.
- Troubleshooting time for configuration-related issues is reduced by 50% through immediate delta visibility.

