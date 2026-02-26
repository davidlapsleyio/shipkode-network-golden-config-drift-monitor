# Implementation Plan: Fleet Drift Audit Service




## Overview




The implementation strategy follows a domain-first approach, prioritizing the Regex Masking Engine and Domain Models to establish a 'source of truth' for comparison logic before building orchestration layers. This ensures that false-positive reduction—a critical KPI—is baked into the foundation. We will then implement the Vendor Adapters and Health Scoring logic to enable data ingestion, followed by the Orchestration Engine to handle parallel execution and historical persistence.

The final phases focus on high-performance 'Pre-Flight' checks and observability. By building the Masking Engine first, we guarantee that the Audit Orchestrator and Pre-Flight checks share identical comparison logic, maintaining consistency across the platform. Verification is performed incrementally via property-based tests to validate masking invariance and scoring integrity.




## Tasks




- [ ] F3-T1. Domain Layer: Masking & Comparison logic

- [ ] F3-T1.1 Implement Domain Models and Masking Engine logic

- Create `src/domain/models.py` with `DriftReport`, `DeviceConfig`, and `MaskingPattern` dataclasses.

- Implement `src/domain/masking_engine.py` using a compiled regex registry.

- Function: `mask_content(content: str, patterns: List[MaskingPattern]) -> str`.

- Function: `calculate_diff(actual: str, intended: str, patterns: List[MaskingPattern]) -> DriftReport`.

- _Requirements: 2_


- [ ] F3-T1.2 Property Test: Masking Invariance Verification

- Create `tests/property/test_masking_invariance.py`.

- Use Hypothesis to generate random strings with embedded timestamps/dynamic data.

- Assert that registered patterns result in zero-diff DriftReports.

- _Requirements: 2_



- [ ] F3-T2. Adapters & Scoring Logic

- [ ] F3-T2.1 Implement Multi-Vendor Device Adapters

- Define `BaseDeviceAdapter` abstract class in `src/adapters/device_adapter.py`.

- Implement `SSHDeviceAdapter` and `RestDeviceAdapter` with timeout controls.

- Ensure `fetch_config()` returns standardized strings.

- _Requirements: 1_


- [ ] F3-T2.2 Implement Fleet Health Scoring System

- Create `src/usecases/health_scorer.py`.

- Class `FleetHealthScorer`.

- Logic: `(compliant_devices / total_reachable_devices) * 100`.

- Handle edge cases where `total_reachable_devices` is zero.

- _Requirements: 3_


- [ ] F3-T2.3 Property Test: Health Score Integrity

- Create `tests/property/test_health_integrity.py`.

- Verify total visibility requirements (must fail if a reachable device is omitted).

- Assert boundary conditions: [0, 100].

- _Requirements: 3_



- [ ] F3-T3. Orchestration & Performance Optimization

- [ ] F3-T3.1 Implement Audit Orchestration Engine & Persistence

- Implement `src/usecases/audit_orchestrator.py` using `asyncio` or `ThreadPoolExecutor`.

- Function `trigger_fleet_audit()`: Pulls intended state, fetches actual, masks, and stores diff.

- Integrate historical tracking in a database (e.g., SQLite/Postgres) in `src/infrastructure/history_repo.py`.

- _Requirements: 1, 4_


- [ ] F3-T3.2 Implement Pre-Flight Compliance Verification

- Create `src/usecases/preflight_check.py`.

- Implement targeted check: fetches only specific device data with a rigorous 30s timeout context.

- Optimize connection reuse to minimize handshake overhead.

- _Requirements: 5_


- [ ] F3-T3.3 Property Test: Pre-Flight Bound Enforcement Check

- Execute bench scripts in `tests/performance/test_preflight_timer.py`.

- Measure T for 100 simulated 'snowflaked' devices.

- Assert T < 30s for 95th percentile.

- _Requirements: 5_



- [ ] F3-T4. Final Checkpoint

- [ ] F3-T4.1 Final System Checkpoint and Integration Verification

- Run `pytest tests/` covering unit, integrated, and property tests.

- Verify masking reduces false positives on a sample dataset with 'routing updates'.

- Confirm health score reflects reachable nodes accurately.




## Notes




- Tasks marked with (*) are optional optimizations for very large fleets.

- Traceability: Requirement IDs are linked to ensure every business need is addressed by a specific dev task.

- Ordering: We follow a domain-driven approach, building the Masking Engine first as it is the core logic for all audit comparisons.

- Compatibility: All new adapters must implement the BaseDevice interface to ensure the Orchestrator remains vendor-agnostic.

