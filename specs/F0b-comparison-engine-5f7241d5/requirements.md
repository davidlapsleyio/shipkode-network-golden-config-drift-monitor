# Requirements Document




## Introduction




The Comparison Engine (F0b) is the core analytical component of Amazon NetGuard, designed to perform high-speed, line-by-line differential analysis between live network configurations and established Golden Configs. In large-scale enterprise environments, manual auditing is hindered by 'noise'—dynamic data such as timestamps, counter values, and ephemeral routing updates that trigger false positives. This feature implements a logic layer that applies Masking Profiles to these data streams, ensuring that only meaningful structural and security-critical discrepancies are reported. By automating the identification of configuration drift while filtering out non-essential changes, the engine enables zero-touch compliance monitoring and provides a reliable 'Source of Truth' for network operations.




## Requirements




### Requirement 1: Core Line-By-Line Diffing




**User Story:** As a Resource-Constrained Operator, I want the system to perform a precise line-by-line comparison of my device config against a Golden Config, so that I can see exactly where my device has drifted from the standard.




#### Acceptance Criteria




1. WHEN the engine compares two configuration blobs, THE system SHALL identify added, deleted, and modified lines with a precision of 100%.

2. WHEN a comparison is triggered, THE system SHALL generate a side-by-side delta report within 5 seconds for configuration blobs up to 10,000 lines.

3. IF a line matches an entry in both blobs exactly, THEN THE system SHALL mark that line as 'Compliant' in the internal state metadata.



### Requirement 2: Regex-Based Variable Masking




**User Story:** As a Scale-Oriented Architect, I want to apply regular expression masks to dynamic data like timestamps, so that I can eliminate false-positive alerts in my compliance reports.




#### Acceptance Criteria




1. WHEN a Masking Profile is active, THE system SHALL ignore any line or substring that matches the defined Regular Expression during the comparison phase.

2. IF a dynamic variable such as a timestamp is detected and matches a mask, THEN THE system SHALL exclude this difference from the final Delta Report score.

3. THE system SHALL support at least 50 concurrent Regex patterns per Masking Profile without exceeding a processing latency of 10 seconds.



### Requirement 3: Drift Metrics Generation




**User Story:** As a Compliance Architect, I want a numerical health score for each device comparison, so that I can provide 100% visibility of our fleet-wide compliance status to leadership.




#### Acceptance Criteria




1. WHEN a comparison is completed, THE system SHALL calculate a Compliance Score as a percentage of lines matching the Golden Config after masking logic is applied.

2. IF the Compliance Score falls below a user-defined threshold (e.g., 95%), THEN THE system SHALL flag the device as 'Non-Compliant' in the fleet dashboard.

3. THE system SHALL include the total count of 'Lines of Drift' (additions and deletions) in the output metadata.



### Requirement 4: Configuration Normalization




**User Story:** As an Operational Auditor, I want the system to ignore whitespace and line-ending differences, so that I don't waste time investigating trivial formatting changes that don't affect device behavior.




#### Acceptance Criteria




1. IF the engine detects that the Source of Truth and the Live Config use different line-ending formats (CRLF vs LF), THEN THE system SHALL normalize the text to a single format before initiating the diff.

2. THE system SHALL strip leading and trailing whitespace from each line before applying comparison logic to prevent white-space-based false positives.



