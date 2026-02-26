# Requirements Document




## Introduction




Amazon NetGuard's Regex Masking Profiles feature addresses the critical challenge of 'noise' in network configuration auditing. In enterprise environments, standard device configurations contain dynamic data—such as system uptime, BGP neighborship timers, and fluctuating timestamps—that change constantly without representing an actual policy violation. This requirement defines a robust framework for creating, managing, and applying advanced regular expression (Regex) patterns to configurations. By filtering out these volatile variables during the comparison process, NetGuard ensures that 'Golden Config' audits only trigger alerts for genuine unauthorized changes, significantly reducing false positives and allowing Network Architects to maintain a high-signal security posture across heterogeneous fleets (Cisco, Juniper, etc.).




## Requirements




### Requirement 1: Masking Profile Creation and Templating




**User Story:** As a Scale-Oriented Architect, I want to create reusable masking templates based on OS types so that I can efficiently standardize noise reduction across my entire heterogeneous fleet.




#### Acceptance Criteria




1. WHEN a user creates or updates a Masking_Profile, THE NetGuard_Engine SHALL validate that all Regex_Patterns are RFC-compliant and do not contain catastrophic backtracking sequences.

2. IF a user selects a pre-defined OS_Template, THEN the system SHALL automatically populate the Masking_Profile with manufacturer-specific Regex_Patterns for common dynamic fields.

3. THE system SHALL support a minimum of 50 unique Regex_Patterns per individual Masking_Profile.



### Requirement 2: Regex-Based Data Redaction Engine




**User Story:** As a Compliance Architect, I want to automatically redact timestamps and dynamic IPs during audits so that I can eliminate false-positive drift alerts caused by non-critical data.




#### Acceptance Criteria




1. WHEN an Audit_Job is executed, THE NetGuard_Engine SHALL apply the assigned Masking_Profile to both the Live_Config and the Golden_Config before performing the diffing logic.

2. IF a character sequence matches a Regex_Pattern defined in the profile, THEN THE NetGuard_Engine SHALL replace that sequence with a standardized [MASKED] placeholder in the audit report.

3. THE masking process SHALL occur in-memory during the audit and SHALL NOT alter the original Golden_Config stored in the database.



### Requirement 3: Interactive Pattern Validation Tool




**User Story:** As an Operational Auditor, I want to test my regex patterns against sample configuration data before deployment so that I can ensure I am not accidentally masking critical security parameters.




#### Acceptance Criteria




1. WHEN a user is configuring a Regex_Pattern, THE system SHALL provide a 'Test Regex' utility that allows the user to input a sample configuration string and view the masked output in real-time.

2. IF the provided Regex_Pattern results in a syntax error, THEN THE system SHALL display a specific error message identifying the invalid character index.



### Requirement 4: Drift Report Masking Visualization




**User Story:** As a Resource-Constrained Operator, I want to toggle between masked and unmasked views in my diff report so that I can troubleshoot the root cause of an issue when dynamic data provides necessary context.




#### Acceptance Criteria




1. WHEN a Drift_Report is generated, THE UI SHALL display a toggle to 'Show/Hide Masked Values' to allow engineers to see the underlying data if needed for deep troubleshooting.

2. IF the 'Hide Masked Values' mode is active, THEN all redactions shall be replaced by a non-selectable gray block to prevent accidental copy-pasting of volatile data.



