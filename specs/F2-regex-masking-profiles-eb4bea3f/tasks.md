# Implementation Plan: Regex Masking Profiles




## Overview




The implementation follows a domain-driven approach, prioritizing the Regex Redaction Engine and Profile models to establish a stable foundation for data transformation. By building the core engine first, we ensure that subsequent features like the Interactive Validator and the Mask-Aware Diff Engine have a reliable API to consume. 

The strategy focuses on an incremental roll-out: starting with the pure domain logic for mask application, followed by the validation services to safeguard against ReDoS and logic errors, and finally integrating these into the existing diffing infrastructure. This ordering minimizes the risk of breaking existing audit flows while providing immediate value through the validation tool.




## Tasks




- [ ] F2-T1. Domain Layer: Masking Models & Engine

- [ ] F2-T1.1 Define Masking Domain Models & Core Engine

- Create `MaskingProfile` and `MaskingRule` dataclasses in `src/domain/masking_engine.py`.

- Implement `MaskingEngine` class with a `mask(text: str, profile: MaskingProfile)` method.

- Add support for OS-type tagging on profiles to allow template association.

- Implement specialized Regex handling using `re.sub` with pre-compiled patterns for performance.

- _Requirements: 1, 2_


- [ ] F2-T1.2 Verify Data Integrity Invariant

- Implement property-based tests in `tests/domain/test_masking_properties.py`.

- Verify that masked output length follows predictable patterns relative to replacement tokens.

- Ensure the engine handles null/empty strings gracefully.



- [ ] F2-T2. Usecase Layer: Pattern Validation Tool

- [ ] F2-T2.1 Implement Interactive Validation Service

- Create `src/usecases/validate_pattern.py`.

- Implement `PatternValidatorService` with a `validate_match` method.

- Integrate a timeout mechanism (e.g., using `signal` or `concurrent.futures`) to kill long-running regex executions.

- Return structured results: `is_valid`, `match_indices`, `execution_time_ms`, and `error_message`.

- _Requirements: 3_


- [ ] F2-T2.2 Verify Validator Responsiveness and Safety

- Create `tests/usecases/test_validator_safety.py`.

- Test with catastrophic backtracking patterns (e.g., `(a+)+$`) to ensure 500ms enforcement.

- Verify SyntaxError handling for malformed regex.



- [ ] F2-T3. Adapter Layer: Diff Engine Integration

- [ ] F2-T3.1 Implement Mask-Aware Diff Engine

- Split `src/adapters/diff_engine.py` into `BaseDiffEngine` and `MaskingDiffDecorator`.

- Implement `MaskAwareDiffEngine` that accepts a `MaskingProfile`.

- The engine must perform two passes if needed or mask buffers before diffing to ensure noise reduction.

- Add a `meta` field to diff segments indicating if a line contains masked data.

- _Requirements: 2, 4_


- [ ] F2-T3.2 Verify Noise Reduction Correctness

- Implement tests comparing diff outputs with and without masking profiles.

- Assert that any change detected in a masked diff is also present in the unmasked version.

- Verify that dynamic content (timestamps) no longer triggers a 'changed' status when masked.



- [ ] F2-T4. Checkpoint: System Integration & QA

- [ ] F2-T4.1 End-to-End Integration Checkpoint

- Run full test suite including property tests and integration tests.

- Verify OS-based template retrieval logic works with the core engine.

- Manually audit a sample 'Drift Report' to ensure toggleable masked/unmasked views correctly render.

- _Requirements: 1, 4_




## Notes




- Tasks marked with (*) are optional UI-enhancements but highly recommended for UX.

- Traceability is maintained via requirement_refs to ensure every business need is mapped to a code change.

- The 'Split-First' strategy is used in Phase 3 to ensure the Diff Engine doesn't become a God Object.

- Backward compatibility: The Masking Engine will default to an 'Identity' profile (no masking) if none is provided to ensure existing diff logic remains functional.

