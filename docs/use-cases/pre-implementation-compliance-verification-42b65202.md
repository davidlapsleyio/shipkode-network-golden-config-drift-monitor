# Pre-Implementation Compliance Verification

As a Resource-Constrained Operator, I want to verify device compliance in under 30 seconds before making changes so that I avoid compounding errors on legacy 'snowflaked' devices.

## Actors

- Resource-Constrained Operator

## Preconditions

- The device is reachable via the management API.
- Golden Configuration for the specific branch site is defined.

## Main Flow

1. Operator selects a specific branch device from the inventory list.
2. Operator triggers a 'Pre-Flight Check' before implementing a manual change.
3. The tool fetches the current running configuration and the Golden Config.
4. The tool performs a line-by-line diff and displays only the deviations.
5. Operator reviews the diff to ensure the device is starting from a known-good state.

## Postconditions

- Operator has a verified baseline for the device configuration.
- All existing 'snowflaked' lines are identified before new changes are applied.

## Acceptance Criteria

- Comparison report is generated in under 30 seconds.
- The diff clearly highlights specific lines added, removed, or modified.
- The report verifies that the configuration matches the 'Source of Truth' before proceeding.

