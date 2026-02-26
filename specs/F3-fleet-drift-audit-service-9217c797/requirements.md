# Requirements Document




## Introduction




The Fleet Drift Audit Service (F3) is a core orchestration component of Amazon NetGuard designed to automate the detection of configuration discrepancies across large-scale enterprise networks. By shifting away from manual SSH-based inspections, this service provides a centralized mechanism to trigger parallel audits, applying regex-based masking to eliminate noise from dynamic data such as timestamps or routing table updates. The service achieves a comprehensive view of network integrity by comparing live state against 'Golden Configurations,' ultimately producing a fleet-wide health score and granular delta reports. This enables Network Architects and Operators to identify unauthorized changes in minutes rather than hours, ensuring that mission-critical infrastructure remains compliant with organizational standards.




## Requirements




### Requirement 1: Parallel Fleet Audit Orchestration




**User Story:** As a Scale-Oriented Architect, I want to execute automated fleet-wide audits so that I can provide 100% visibility of compliance to the CISO without manual overhead.




#### Acceptance Criteria




1. WHEN an audit is triggered for a fleet, THE Fleet_Drift_Audit_Service SHALL initiate parallel configuration fetches across multiple Target_Devices using management APIs.

2. IF the fleet size exceeds 1,000 devices, THEN THE Fleet_Drift_Audit_Service SHALL limit concurrent connections to a maximum of 50 simultaneous threads to prevent API rate limiting.

3. WHEN the audit completes, THE Fleet_Drift_Audit_Service SHALL update the Audit_Log with the success or failure status for every device in the scope.



### Requirement 2: Regex-Based Variable Masking and Comparison




**User Story:** As a Compliance Architect, I want to mask dynamic variables like timestamps and routing updates so that I can reduce false-positive alerts by 95%.




#### Acceptance Criteria




1. WHEN comparing live configurations, THE Fleet_Drift_Audit_Service SHALL apply the assigned Masking_Profile to filter out dynamic strings defined by Regex.

2. IF a live configuration line matches a Masking_Profile pattern, THEN THE Fleet_Drift_Audit_Service SHALL exclude that line from the Delta_Report generation.

3. WHEN the comparison is finished, THE Fleet_Drift_Audit_Service SHALL generate a side-by-side Diff_View highlighting only unmasked discrepancies between the live config and the Golden_Config.



### Requirement 3: Fleet Health Score Calculation




**User Story:** As a Scale-Oriented Architect, I want a single fleet health percentage so that I can quickly assess the overall compliance level of the entire network.




#### Acceptance Criteria




1. WHEN all devices in an audit batch have been processed, THE Fleet_Drift_Audit_Service SHALL calculate the Fleet_Health_Score as the percentage of devices where zero unmasked drift was detected.

2. IF a device is unreachable or the API call fails, THEN THE Fleet_Drift_Audit_Service SHALL exclude that device from the health score numerator and mark it as 'Indeterminate' in the summary.

3. WHEN the calculation is complete, THE Fleet_Drift_Audit_Service SHALL publish the Fleet_Health_Score to the executive dashboard.



### Requirement 4: Historical Drift Tracking and Root Cause Analysis




**User Story:** As an Operational Auditor, I want to access historical records of when drift occurred so that I can decrease troubleshooting time for configuration-related outages.




#### Acceptance Criteria




1. WHEN a user selects a specific device drift event, THE Fleet_Drift_Audit_Service SHALL retrieve the Historical_Drift_Log for that device.

2. THE Historical_Drift_Log SHALL include the timestamp of detection, the specific lines of configuration that drifted, and the change user ID if available from the management API.

3. IF drift is detected over multiple audit cycles, THEN THE Fleet_Drift_Audit_Service SHALL provide a chronological timeline of changes for that specific device.



### Requirement 5: Pre-Flight Compliance Verification




**User Story:** As a Resource-Constrained Operator, I want to verify device compliance in under 30 seconds before making changes so that I avoid compounding errors on legacy 'snowflaked' devices.




#### Acceptance Criteria




1. WHEN an operator initiates a Pre-Flight_Check for a single device, THE Fleet_Drift_Audit_Service SHALL prioritize this request over scheduled bulk audits.

2. THE Fleet_Drift_Audit_Service SHALL return the line-by-line Delta_Report for a Pre-Flight_Check in under 30 seconds.

3. IF the device is found to be in a 'Drifted' state during the check, THEN THE Fleet_Drift_Audit_Service SHALL return a 'Caution' status to the operator before any configuration changes are applied.



