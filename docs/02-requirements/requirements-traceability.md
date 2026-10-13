# AgentLens — Requirements Traceability Matrix

**Document ID:** AL-RTM-001  
**Version:** 0.1  
**Status:** Draft  
**Parent Document:** AL-SRS-001 — Software Requirements Specification

## 1. Purpose

The Requirements Traceability Matrix (RTM) establishes traceability between the requirements defined in the Software Requirements Specification (SRS), their acceptance criteria, planned test cases, and verification status.

It helps ensure that every requirement is implemented, tested, and verified before the initial release is accepted.

## 2. Functional Requirements Traceability

| Requirement ID | Requirement | Test Case ID | Verification Method | Status |
|---|---|---|---|---|
| FR-01 | Accept natural-language requests | TC-01 | Input validation test | Planned |
| FR-02 | Retrieve graph schema context | TC-02 | Schema integration test | Planned |
| FR-03 | Generate candidate Cypher | TC-03 | Query generation test | Planned |
| FR-04 | Validate generated queries | TC-04 | Query validation test | Planned |
| FR-05 | Enforce read-only execution | TC-05 | Security test | Planned |
| FR-06 | Execute permitted queries | TC-06 | Database integration test | Planned |
| FR-07 | Present results | TC-07 | Result formatting test | Planned |
| FR-08 | Search by name and brand | TC-08 | Functional search test | Planned |
| FR-09 | Filter by rating and price | TC-09 | Numeric filter test | Planned |
| FR-10 | Handle errors | TC-10 | Negative and failure-path tests | Planned |
| FR-11 | Record diagnostic information | TC-11 | Logging and redaction test | Planned |
| FR-12 | Support repeatable evaluation | TC-12 | Evaluation reproducibility test | Planned |

## 3. Non-Functional Requirements Traceability

| Requirement ID | Requirement | Verification Method | Acceptance Target | Status |
|---|---|---|---|---|
| NFR-01 | Security | Security test suite | Zero unauthorized writes executed | Planned |
| NFR-02 | Correctness | Fixed evaluation dataset | At least 85% expected-result correctness | Planned |
| NFR-03 | Reliability | Dependency failure tests | Controlled handling of anticipated failures | Planned |
| NFR-04 | Performance | Latency measurement | p95 below 5 seconds under documented conditions | Planned |
| NFR-05 | Maintainability | Architecture and code review | Required components separated into testable modules | Planned |
| NFR-06 | Testability | Automated coverage report | At least 80% line coverage for core backend logic | Planned |
| NFR-07 | Usability | User-interface acceptance checklist | Clear input, results, empty states, and errors | Planned |
| NFR-08 | Reproducibility | Independent setup verification | Documented workflow can be reproduced | Planned |

## 4. Test Case Register

| Test Case ID | Description | Expected Outcome |
|---|---|---|
| TC-01 | Submit valid, empty, and excessively long inputs | Valid requests are accepted; invalid inputs are handled appropriately |
| TC-02 | Retrieve the graph schema | Current supported labels, relationships, and properties are supplied |
| TC-03 | Generate Cypher for supported product questions | Candidate queries reflect the requested filters |
| TC-04 | Submit invalid syntax and unsupported schema references | Invalid candidates are rejected before execution |
| TC-05 | Attempt write operations and disallowed query constructs | Unauthorized operations are rejected and never executed |
| TC-06 | Execute a valid read-only query against Neo4j | Expected structured results are returned |
| TC-07 | Format non-empty and empty query results | Results and empty states are distinguishable |
| TC-08 | Search using product names and brands | Expected matching records are returned |
| TC-09 | Apply rating and price filters, including missing values | Correct records are returned without treating missing prices as zero |
| TC-10 | Simulate model errors, database errors, and timeouts | Controlled errors are returned without exposing sensitive information |
| TC-11 | Trigger application failures and inspect logs | Useful diagnostic events are recorded without secrets |
| TC-12 | Run the evaluation suite repeatedly | Test inputs, expected outcomes, and measured results are recorded reproducibly |

## 5. Traceability Rules

1. Every functional and non-functional requirement must have a verification method.
2. Each implemented feature must be linked to its corresponding test cases.
3. Test cases must have explicit expected outcomes before execution.
4. Failed tests must be recorded and investigated rather than silently removed.
5. Changes to requirements must be reflected in this matrix and the corresponding test documentation.
6. Verification status must reflect actual evidence, not implementation assumptions.

## 6. Verification Status Definitions

- **Planned:** Test or verification activity has not yet been completed.
- **Implemented:** The associated feature or test has been developed but not fully verified.
- **Passed:** The defined verification has passed and evidence has been recorded.
- **Failed:** The verification did not meet its acceptance criteria.
- **Blocked:** Verification cannot proceed because a dependency or prerequisite is unavailable.

## 7. Current Status

This matrix is a draft. All test cases are planned; no implementation or verification results are claimed by this document.

The RTM will be updated as requirements are refined, test cases are implemented, and evidence becomes available.

**Next artifact:** AL-UC-001 — Use Case Specifications and Diagram.