# Requirements Document




## Introduction




The API Client Orchestrator serves as the foundational communication layer for Amazon NetGuard, enabling secure, authenticated, and high-performance interaction with external Network Management Systems (NMS). Its primary purpose is to abstract the complexities of diverse vendor APIs into a unified Python/FastAPI interface. By managing connection pooling, OAuth2 token lifecycles, and asynchronous data retrieval, this module allows NetGuard to fetch live 'running-configs' from thousands of devices simultaneously. This eliminates the need for manual SSH-based audits, providing the real-time visibility required to detect configuration drift across a global enterprise fleet in under 30 seconds.




## Requirements




### Requirement 1: Secure Multi-Tenant Authentication Management




**User Story:** As a Scale-Oriented Architect, I want the system to securely manage NMS credentials and tokens so that I can maintain persistent, authenticated access to the fleet without manual re-authentication.




#### Acceptance Criteria




1. WHEN an authentication request is initiated, THE Orchestrator SHALL utilize the OAuth2 client credentials flow to obtain a JWT from the target NMS.

2. IF an access token is expired or within a 300-second buffer of expiration, THEN THE Orchestrator SHALL execute a refresh token flow to maintain session continuity.

3. THE Orchestrator SHALL encrypt all NMS_Credentials at rest using AES-256 before storing them in the persistent database.



### Requirement 2: Asynchronous Config Retrieval Engine




**User Story:** As a Compliance Architect, I want to fetch configurations from thousands of devices in parallel so that my weekly audits are completed in minutes rather than hours.




#### Acceptance Criteria




1. WHEN a fleet audit is triggered, THE Orchestrator SHALL execute asynchronous HTTP GET requests using an IO-bound concurrency pool.

2. THE Orchestrator SHALL implement a retry mechanism with exponential backoff if a 429 (Too Many Requests) or 5xx error is received from the NMS_API.

3. IF the Fetch_Timeout of 25 seconds is exceeded for a single device, THEN THE Orchestrator SHALL log a 'RequestTimeout' status and continue with the remaining queue items.



### Requirement 3: NMS Response Parsing and Normalization




**User Story:** As an Operational Auditor, I want the system to extract clean configuration text from complex API responses so that I can perform accurate line-by-line diffing.




#### Acceptance Criteria




1. WHEN the NMS_API returns a payload, THE Orchestrator SHALL parse the JSON and extract the specific 'running-config' string based on a predefined provider-specific schema.

2. IF the returned payload is missing the 'config_payload' field, THEN THE Orchestrator SHALL raise a 'MalformedResponseError' and update the device status to 'Failed'.

3. THE Orchestrator SHALL sanitize the Running_Config string by removing non-printable ASCII characters before passing it to the Masking_Engine.



### Requirement 4: On-Demand Pre-Flight Fetching




**User Story:** As a Resource-Constrained Operator, I want to fetch a live config on-demand before making a change so that I can verify the device state in under 30 seconds.




#### Acceptance Criteria




1. WHEN the Pre-Flight_Check endpoint is called, THE Orchestrator SHALL prioritize that specific DeviceID at the head of the request queue.

2. THE Orchestrator SHALL return the full Running_Config to the calling service in under 15 seconds for a single-device request.

3. IF the NMS_API is unreachable, THEN THE Orchestrator SHALL return a 503 Service Unavailable error with a 'NetworkUnreachable' diagnostic message.



