# Implementation Plan: Comparison Engine




## Overview




The implementation follows a domain-driven approach, starting with the core metrics logic and normalization primitives before building the application-level orchestration. We prioritize the 'Drift Metrics Calculator' in the domain layer to establish a stable contract for what constitutes a 'match' versus a 'difference' before implementing complex masking or normalization logic.

In the second phase, we implement the Pre-processor Engine to handle whitespace and regex masking. This ordering allows us to test the normalization logic against the already-verified metrics engine. Finally, the Comparison Orchestrator is built to glue these components together, providing a high-level API for line-by-line diffing. This incremental strategy ensures that each layer is validated against its specific correctness properties (e.g., Whitespace Invariance) before moving to the next level of abstraction.




## Tasks




- [ ] F0b-T1. Phase 1: Domain Metrics & Scoring

- [ ] F0b-T1.1 Implement Core Drift Metrics Logic

- Create src/domain/metrics.py.

- Implement 'DriftResult' dataclass containing total_lines, matched_lines, and health_score.

- Implement 'calculate_health_score(matches, total)' function.

- Ensure floating point precision handling.

- Verification: Unit test with edge cases (0 lines, 100% match).

- _Requirements: 3_


- [ ] F0b-T1.2 Property Test: Metrics Boundedness

- Create tests/domain/test_metrics_properties.py.

- Implement Hypothesis or standard property-based tests.

- Verify that for any input, score is 0 <= score <= 100.



- [ ] F0b-T2. Phase 2: Normalization & Masking Engine

- [ ] F0b-T2.1 Configuration Normalization Logic

- Create src/usecases/normalizer.py.

- Implement 'NormalizationEngine' class.

- Add 'strip_whitespace(text: str)' and 'standardize_line_endings(text: str)' methods.

- Verification: Compare strings with \r\n vs \n and trailing spaces.

- _Requirements: 4_


- [ ] F0b-T2.2 Regex-Based Variable Masking

- Implement 'apply_regex_masks(text: str, masks: List[str])' in 'NormalizationEngine'.

- Use 're.sub' to replace masked patterns with a static placeholder (e.g., '<MASKED>').

- Verification: Define mask for 'timestamp: \d+' and compare two strings with different dates.

- _Requirements: 2_


- [ ] F0b-T2.3 Property Test: Normalization Invariants

- Implement property tests for 'NormalizationEngine'.

- Test that whitespace variations of the same content yield identical normalized output.

- Test that masked values produce identical output if the mask matches both.



- [ ] F0b-T3. Phase 3: Logic Checkpoint

- [ ] F0b-T3.1 Checkpoint: Core Logic Verification

- Execute 'pytest tests/domain/'.

- Verify all normalization and metric properties pass with high coverage (>95%).

- Ensure no circular dependencies between metrics and normalizers.



- [ ] F0b-T4. Phase 4: Orchestration & Integration

- [ ] F0b-T4.1 Implement Comparison Orchestrator

- Create src/usecases/comparison_engine.py.

- Implement 'ComparisonOrchestrator' class.

- Implement 'compare(source: str, target: str, masks: List[str]) -> DiffReport'.

- Integrate DiffLib for line-by-line delta generation.

- Map DiffLib outputs to the 'DriftResult' domain model.

- Verification: Integration test with sample router configs.

- _Requirements: 1, 3_


- [ ] F0b-T4.2 Line-By-Line Diffing & Reporting

- Implement 'generate_diff_report' to return line numbers and types (+/-/ ).

- Ensure masked lines are reported as matches in the final diff UI.

- _Requirements: 1_



- [ ] F0b-T5. Phase 5: Final Validation

- [ ] F0b-T5.1 Final Checkpoint: Full Feature Validation

- Run full test suite including orchestrator integration tests.

- Verify that a config with only timestamp changes and whitespace returns 100% health score.

- _Requirements: 1, 2, 3, 4_




## Notes




- Tasks marked with (*) are optional optimizations for very large configuration files.

- Requirement and Property traceability ensures every line of code serves a business goal and safety invariant.

- The 'Domain-First' constraint ensures the metrics logic is decoupled from the specific regex libraries or file formats used.

- Backward compatibility: The comparison engine is designed as a pure-function library to ensure it can be integrated into existing batch jobs without stateful side effects.

