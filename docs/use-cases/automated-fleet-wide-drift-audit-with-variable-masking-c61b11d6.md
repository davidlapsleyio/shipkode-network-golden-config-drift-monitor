# Automated Fleet-Wide Drift Audit with Variable Masking

As a Scale-Oriented Architect, I want to execute automated weekly audits that mask dynamic data so that I can reduce false-positive alerts by 95% and provide 100% visibility to the CISO.

## Actors

- Scale-Oriented Architect
- Network Management API

## Preconditions

- Golden Configurations for all device models are stored in the repository.
- API credentials for the network management platform are configured and valid.

## Main Flow

1. Architect defines Regex-based masking patterns for dynamic variables like timestamps and BGP peer IPs.
2. The tool initiates a bulk configuration pull from the management API for all 4,000 devices.
3. The tool compares each live configuration against the version-controlled Golden Configuration.
4. The tool ignores lines matching the defined masking patterns to prevent false positives.
5. The tool generates a fleet-wide compliance health score and detailed drift report.

## Postconditions

- A comprehensive drift report is available via the dashboard.
- Alerting system notifies the Architect of any non-compliant devices.

## Acceptance Criteria

- Drift analysis for 4,000 devices completes in less than 15 minutes.
- Variable masking rules filter 100% of timestamps and dynamic uptime strings.
- System generates a machine-readable JSON report for downstream CISO dashboards.

