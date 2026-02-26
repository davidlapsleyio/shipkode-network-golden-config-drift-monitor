# Implementation Plan: Golden Config Repository




## Overview




The implementation follows a domain-driven, bottom-up strategy. We first establish the core data structures for Golden Configs and Versioning in the domain layer to ensure 'Version Immutability' is baked into the model level. This provides a stable foundation for the Device Group Association engine, which handles the logic for mapping configurations to the fleet.

Once the domain logic is solidified, we implement the application use cases for lifecycle management and diffing. Only after the business logic is verified through property-based testing do we expose the functionality via Infrastructure/API layers. This ensures that the 'Standard of Truth' is protected from external implementation details and that compliance auditing is based on a verified logic heart.




## Tasks




- [ ] F1-T1. Domain Models and Repository Interfaces

- [ ] F1-T1.1 Define Immutable GoldenConfig Models

- Create 'src/domain/models/golden_config.py' with 'GoldenConfig' dataclass.

- Include fields: 'id', 'group_id', 'version', 'config_payload', 'created_at'.

- Implement '__post_init__' to enforce immutability on payload strings.

- _Requirements: 1_


- [ ] F1-T1.2 Define Device Group and Association Models

- Create 'src/domain/models/device_group.py'.

- Implement 'DeviceGroup' class with 'id', 'name', and 'criteria' fields.

- Define 'Association' entity linking 'DeviceID' to 'GroupID'.

- _Requirements: 2_


- [ ] F1-T1.3 Define Config Repository Interface

- Create 'src/domain/interfaces/config_repository.py'.

- Define abstract methods: 'save(config)', 'get_latest(group_id)', 'get_version(group_id, version_id)'.

- Define 'find_group_by_device(device_id)'.

- _Requirements: 1, 2_



- [ ] F1-T2. Checkpoint: Domain Integrity and Property Verification

- [ ] F1-T2.1 Property Test: Version Immutability Validation

- Write property-based tests in 'tests/domain/test_config_properties.py'.

- Use Hypothesis to verify that any 'update' call results in a new object with a higher version number rather than modifying the existing instance.


- [ ] F1-T2.2 Property Test: Complete Association Coverage Validation

- Implement a test suite verifying that every mocked Device ID returns exactly one Association record.

- Validate that attempts to create overlapping associations are caught at the domain level.



- [ ] F1-T3. Usecase Layer Implementation

- [ ] F1-T3.1 Implement Device Group Association Engine

- Create 'src/usecases/association_engine.py'.

- Implement 'AssociationEngine.get_effective_config(device_id)' logic.

- Ensure it crawls group hierarchies if applicable to find the 'Standard of Truth'.

- _Requirements: 2_


- [ ] F1-T3.2 Implement Lifecycle and Differencing Logic

- Create 'src/usecases/config_lifecycle.py'.

- Implement 'ConfigManager.create_new_version(group_id, new_content)'.

- Implement 'ConfigManager.get_diff(version_a, version_b)' using a standard diffing library (e.g., difflib).

- _Requirements: 3_


- [ ] F1-T3.3 Property Test: Diff Consistency Validation

- Create property tests to verify that Diff(V1, V2) + Diff(V2, V3) == Diff(V1, V3) logic holds across various config payloads.

- Ensure the diffing engine handles empty or identical configurations gracefully.

- _Requirements: 3_



- [ ] F1-T4. Infrastructure and External Integration

- [ ] F1-T4.1 Implement External Audit API Endpoints

- Create 'src/infrastructure/api/v1/audit_routes.py'.

- Implement 'GET /configs/{group_id}/versions' endpoint.

- Implement 'GET /configs/{group_id}/diff/{v1}/{v2}' endpoint.

- Ensure dependency injection of UseCases into FastAPI/Flask routes.

- _Requirements: 4_


- [ ] F1-T4.2 Implement SQL Persistence Adapter

- Create 'src/infrastructure/persistence/sql_config_repo.py'.

- Implement SQLAlchemy or similar mapping for the domain interfaces defined in Phase 1.

- Ensure 'save' operations are wrapped in transactions to maintain version integrity.

- _Requirements: 1_



- [ ] F1-T5. Final Checkpoint and Handover

- [ ] F1-T5.1 Final Integration Checkpoint

- Run 'pytest tests/' to ensure all domain, usecase, and infrastructure tests pass.

- Verify through OpenAPI/Swagger docs that all Phase 4 endpoints are discoverable.

- Perform a manual audit check: create a config, update it, and verify the old version remains unchanged in the DB.




## Notes




- Tasks marked with (*) are optional optimizations for performance.

- Traceability: Requirement and Property references ensure that every line of code serves a business goal or satisfies a safety constraint.

- Ordering Constraint: Domain models (Phase 1) must be finalized before Usecases (Phase 2) to prevent cascading signature changes.

- Backward Compatibility: API Versioning (v1) is used in Phase 4 to ensure future schema changes do not break the External Audit integrations.

