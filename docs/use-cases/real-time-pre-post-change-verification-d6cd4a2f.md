# Real-Time Pre/Post Change Verification

As a Resource-Constrained Operator, I want to verify device compliance in under 30 seconds before and after making changes so that I can prevent accidental outages caused by 'snowflake' configurations.

## Actors

- Resource-Constrained Operator

## Preconditions

- The device is reachable via the Management API.
- A 'Source of Truth' Golden Config is assigned to the device model.

## Main Flow

1. The Operator selects a specific branch router before initiating a scheduled maintenance window.
2. The Operator triggers an ad-hoc 'Pre-Check' scan against the Golden Configuration.
3. The tool fetches the live config and performs a real-time diff.
4. The Operator reviews the diff to identify pre-existing 'snowflakes' or unauthorized changes.
5. The Operator executes the planned change and runs a 'Post-Check' to ensure only intended lines were modified.

## Postconditions

- The device configuration state is verified and logged.
- The Operator has a clear 'before and after' record of the maintenance window.

## Acceptance Criteria

- Comparison completes in < 15 seconds.
- The UI highlights specific added, deleted, or modified lines in a side-by-side view.
- The tool identifies the specific Golden Config version used for comparison.

