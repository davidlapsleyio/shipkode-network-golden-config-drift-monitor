# Implementation Plan: API Client Orchestrator




## Overview




This implementation strategy follows a domain-driven approach, starting with the core data models and interfaces before moving to concrete infrastructure adapters. By defining the 'ConfigResult' and 'Credential' schemas first, we ensure that the orchestrator remains decoupled from the specific JSON formats of external Network Management Systems (NMS). The ordering prioritized building the Auth Manager because it serves as the foundational dependency for all subsequent API communication.

The second phase implements high-concurrency fetching using an asynchronous worker-pool pattern to satisfy strict performance and resource-bounding requirements. Finally, we implement the normalization logic to strip platform-specific metadata, ensuring that the final output is a clean, diffable string. This incremental approach allows for continuous integration testing of the authentication flow before the more complex parallel config retrieval logic is layered on top.




## Tasks




- [ ] F0a-T1. Domain Layer & Schema Definition

- [ ] F0a-T1.1 Define Domain Entities and Models

- Create `src/domain/models.py`.

- Implement `ConfigResult` Pydantic model with fields: device_id, raw_text, cleaned_text, and timestamp.

- Implement `NMSTenant` model for multi-tenant mapping.

- Verification: Validate models with sample high-volume JSON payloads.

- _Requirements: 1, 3_


- [ ] F0a-T1.2 Define Service Interfaces (Ports)

- Create `src/domain/interfaces.py`.

- Define `IAuthManager` abstract base class with `get_token` and `refresh_credentials` methods.

- Define `IConfigClient` abstract base class with `fetch_device_config` (async).

- _Requirements: 1, 2_



- [ ] F0a-T2. Authentication & Identity Adapters

- [ ] F0a-T2.1 Implement Multi-Tenant Auth Manager

- Implement `AuthManager` in `src/adapters/auth_manager.py`.

- Create `_get_stored_credentials` to fetch from secure vault/env.

- Implement token caching with expiration tracking.

- Logic for `refresh_credentials` to handle rotation.

- _Requirements: 1_


- [ ] F0a-T2.2 Implement Self-Healing Auth Logic

- Implement retry decorator in `src/adapters/http_client.py`.

- Logic: Catch 401, trigger `AuthManager.refresh()`, retry once.

- Verification: Unit test with mocked 401 responses.

- _Requirements: 1_



- [ ] F0a-T3. Use Case Orchestration & Normalization Engine

- [ ] F0a-T3.1 Implement Asynchronous Config Retrieval Engine

- Create `src/usecases/orchestrator.py`.

- Use `asyncio.Semaphore(BATCH_SIZE)` to wrap API calls.

- Implement `fetch_fleet_configs(device_ids)` using `asyncio.gather`.

- Verification: Mock 1000 requests and assert concurrent count <= BATCH_SIZE.

- _Requirements: 2, 4_


- [ ] F0a-T3.2 Implement Config Normalization Logic

- Implement `ConfigNormalizer` in `src/usecases/normalizer.py`.

- Create regex/parsing rules to strip 'json_metadata', 'envelope_id', and 'api_version' keys.

- Ensure output is strictly the running-config text.

- Verification: Test with Cisco/Juniper style API envelopes.

- _Requirements: 3_



- [ ] F0a-T4. Verification & Property Testing

- [ ] F0a-T4.1 Property Test: Concurrency Constraints

- Write property-based test in `tests/test_properties.py`.

- Use `Hypothesis` or `pytest-asyncio` to flood the orchestrator.

- Assert that `active_connections` never exceeds `BATCH_SIZE`.


- [ ] F0a-T4.2 Property Test: Auth Resilience (Self-Healing)

- Simulate an expired token scenario.

- Verify exactly one call to the Auth service before the second fetch attempt.

- Assert failure if the second attempt also returns 401.


- [ ] F0a-T4.3 Final Checkpoint: Full Compliance Verification

- Run full suite: `pytest tests/`.

- Verify zero violations in the normalization integrity check with real-world NMS JSON samples.




## Notes




- Tasks marked with (*) are optimization-focused and can be deferred if deadlines are tight.

- Traceability: Sub-tasks link to Requirements (R1-R4) to ensure every feature is accounted for and Properties (P1-P3) to enforce architectural constraints.

- Ordering Constraint: Domain models (Phase 1) must be defined before Adapters (Phase 2) to prevent leaky abstractions from external API formats.

- Compatibility: All internal interfaces use type hints to ensure compatibility with FastAPI dependency injection.

