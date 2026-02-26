# Implementation Plan: Pre-Flight Compliance Check




## Overview




The implementation follows a domain-driven approach, prioritizing the formal definition of policy models and compliance reports to satisfy the 'Blocker Invariant'. By establishing the data structures first, we ensure that severity-based logic (e.g., blocking maintenance on high-priority violations) is baked into the core business logic before integrating external adapters.

The second phase focuses on the Compliance Checker service and the LLM Policy Evaluator adapter. A key component of this phase is ensuring 'Policy Consistency' by pinning the policy versions during the evaluation lifecycle. The final phase involves rigorous verification through property-based testing to ensure that no 'BLOCKER' severity ever results in a compliant status and that state transitions are handled predictably.




## Tasks




- [ ] F4-T1. Domain Model Implementation

- [ ] F4-T1.1 Define Policy and Compliance Entities

- Create/Update src/domain/policy.py.

- Define Violation class with fields: rule_id, description, severity (Enum: LOW, MEDIUM, HIGH, BLOCKER).

- Define ComplianceReport class with fields: device_id, timestamp, violations (List[Violation]), and a computed property is_compliant.

- Implement logic in ComplianceReport.is_compliant: return False if any violation has severity == BLOCKER.

- Verification: Unit test instantiation of report with a BLOCKER violation and assert is_compliant is False.


- [ ] F4-T1.2 Define Policy Versioning Models

- Enhance src/domain/policy.py.

- Add PolicyVersion and ActiveConfig classes to track which policy set is active.

- Ensure ComplianceReport stores a reference or snapshot of the PolicyVersion used.

- Verification: Check that reports reference the specific version ID from the config at time of creation.



- [ ] F4-T2. Checkpoint: Domain Integrity

- [ ] F4-T2.1 Run Domain Unit Tests

- Execute pytest on src/domain/policy.py.

- Verify that a report with only 'HIGH' violations can be compliant but one with 'BLOCKER' cannot.

- Verify that initializing a report without a policy version raises a validation error.



- [ ] F4-T3. Services and Adapters Implementation

- [ ] F4-T3.1 Implement Compliance Checker Service

- Create/Update src/usecases/compliance_checker.py.

- Implement ComplianceCheckerService.run_check(device_id).

- Method must fetch the current 'ActiveConfig' immediately to ensure consistent policy application.

- Orchestrate the call to the evaluator and return the domain ComplianceReport.

- Verification: Mock the evaluator and verify the service passes the correct policy version.


- [ ] F4-T3.2 Implement LLM Evaluator Adapter

- Create/Update src/adapters/llm_evaluator.py.

- Implement LLMPolicyEvaluator class.

- Map LLM output strings to the Violation domain model, specifically ensuring 'blocker' keywords in LLM responses map to Severity.BLOCKER.

- Verification: Test adapter with sample LLM prompts and verify internal parsing logic.



- [ ] F4-T4. Correctness Property Verification

- [ ] F4-T4.1 Property Test: Blocker Invariant Verification

- Use Hypothesis to generate random lists of Violations.

- Test that for every generated ComplianceReport R, R.is_compliant is always False if any violation severity is 'BLOCKER'.

- File: tests/property/test_compliance_invariants.py.


- [ ] F4-T4.2 Property Test: Policy Consistency Verification

- Implement a test that simulates a policy update mid-execution.

- Assert that the ComplianceReport produced matches the version captured at the start of the service call, not the one updated during the call.

- File: tests/property/test_policy_consistency.py.



- [ ] F4-T5. Checkpoint: Final Verification

- [ ] F4-T5.1 Full Integration Test Suite

- Execute full test suite including domain, usecase, and property tests.

- Perform a manual 'Pre-Flight' run using the CLI to ensure end-to-end connectivity from LLM to Repo.




## Notes




- Tasks marked with (*) are optional optimizations for performance and are not strictly required for the initial MVP.

- Traceability is maintained by linking sub-tasks to property_refs; this ensures that every correctness invariant is verified by a corresponding implementation or test task.

- The 'Domain-First' strategy ensures that the logic for severity handling and policy versioning is established before external integrations (like LLMs) are implemented.

- Backward compatibility: The result of the compliance check uses standardized JSON schemas to ensure UI/CLI tools remain compatible across policy updates.

