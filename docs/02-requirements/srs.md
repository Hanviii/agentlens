# AgentLens — Software Requirements Specification

**Document ID:** AL-SRS-001  
**Version:** 0.1  
**Status:** Draft for review  
**Parent Document:** AL-DOC-001 — Project Charter

## 1. Introduction

### 1.1 Purpose

This document specifies the functional and non-functional requirements for AgentLens, a natural-language interface for product discovery using a Neo4j knowledge graph.

It serves as the baseline for system design, implementation, verification, and acceptance testing.

### 1.2 Scope

AgentLens accepts natural-language product discovery requests, generates candidate Cypher queries using an LLM, validates the queries, executes permitted read-only queries against Neo4j, and presents the resulting product information.

### 1.3 Intended Audience

- Project developer and owner.
- Software engineering project evaluator.
- Future maintainers of the application.
- Testers evaluating system behavior and correctness.

### 1.4 Definitions

| Term | Definition |
|---|---|
| LLM | Large Language Model used to generate candidate Cypher queries. |
| Cypher | Query language used to interact with Neo4j. |
| Knowledge graph | Data model consisting of nodes, relationships, and properties. |
| Schema-aware generation | Query generation informed by the available graph structure and properties. |
| Query validation | Checks performed before a generated query is permitted to execute. |
| Semantic correctness | Whether a query returns results that satisfy the user's intended request. |
| RTM | Requirements Traceability Matrix linking requirements to tests. |

## 2. Overall Description

### 2.1 Product Perspective

AgentLens is a software application that connects a user-facing interface to an application backend, an LLM service, and a Neo4j graph database.

The backend is responsible for coordinating query generation, validation, execution, error handling, and result formatting.

### 2.2 User Classes

**End User:** Submits natural-language product discovery requests and views results.

**Developer / Administrator:** Configures application dependencies, manages the environment, and investigates failures.

### 2.3 Operating Environment

The proposed environment consists of:

- Python application backend using FastAPI.
- Neo4j Aura graph database.
- An LLM accessed through a selected model integration.
- React frontend.
- Git and GitHub for version control and project tracking.
- pytest for automated testing.

The final model and deployment configuration will be selected and verified during implementation.

### 2.4 Assumptions and Dependencies

1. The selected LLM must support the required query-generation workflow.
2. Neo4j must be reachable through an appropriately configured database connection.
3. Product attributes may be missing or inconsistent.
4. Product search results are limited to the data available in the graph.
5. Database credentials and model API keys must not be committed to source control.
6. The real-data implementation will remain separate from the existing synthetic-data prototype.

## 3. Functional Requirements

### FR-01: Accept Natural-Language Requests

The system shall accept a natural-language product discovery question from the user.

**Acceptance criteria:**
- A non-empty valid request enters the query-processing workflow.
- Empty input is rejected with an understandable message.
- Excessively long input is handled according to configured limits.

### FR-02: Retrieve Graph Schema Context

The system shall provide the query-generation component with relevant information about available node labels, relationships, and properties.

**Acceptance criteria:**
- The schema context reflects the implemented graph schema.
- Unsupported properties are not presented as available fields.
- Schema retrieval failures are handled explicitly.

### FR-03: Generate Candidate Cypher

The system shall generate a candidate Cypher query that represents the user's request.

**Acceptance criteria:**
- The candidate query uses supported labels, relationships, and properties.
- Required filters are represented in the generated query.
- Generation failures do not cause automatic database execution.

### FR-04: Validate Generated Queries

The system shall validate generated queries before execution.

**Acceptance criteria:**
- Invalid syntax is rejected.
- Unsupported schema references are rejected.
- Unsupported query constructs are rejected.
- Validation failures cannot bypass the execution gate.

### FR-05: Enforce Read-Only Execution

The system shall prevent unauthorized database modifications.

**Acceptance criteria:**
- Write operations and disallowed constructs are rejected.
- Database credentials used by the application have only the permissions required for the permitted operations.
- Security tests confirm that unauthorized writes are not executed.

### FR-06: Execute Permitted Queries

The system shall execute validated read-only queries against Neo4j.

**Acceptance criteria:**
- Valid queries return structured results.
- Database failures produce controlled errors.
- Query execution is subject to configured timeouts and result limits.

### FR-07: Present Results

The system shall present matching products and relevant available attributes in a readable format.

**Acceptance criteria:**
- Product results include identifiers and available descriptive fields.
- Empty result sets are clearly distinguished from execution failures.
- Results are formatted consistently.

### FR-08: Search by Product Name and Brand

The system shall support product-name and brand searches when the corresponding metadata is available.

**Acceptance criteria:**
- Matching searches return the expected records in the evaluation dataset.
- Missing brand values are not treated as real brand names.
- Unsupported search conditions are handled explicitly.

### FR-09: Filter by Rating and Price

The system shall support numeric rating and price filters when the relevant values are available and valid.

**Acceptance criteria:**
- Numeric filters return the expected records in the evaluation dataset.
- Missing prices are not treated as zero.
- Invalid numeric values are handled safely.
- The application distinguishes product metadata ratings from individual user ratings.

### FR-10: Handle Errors

The system shall handle invalid input, generation failures, rejected queries, empty results, and database errors.

**Acceptance criteria:**
- Users receive understandable error messages.
- Internal stack traces, secrets, and credentials are not exposed.
- Failures are logged appropriately for diagnosis.

### FR-11: Record Diagnostic Information

The system shall record sufficient diagnostic information to investigate application failures.

**Acceptance criteria:**
- Logs identify the relevant failure stage.
- Sensitive configuration values are excluded.
- Logging does not unnecessarily retain sensitive user information.

### FR-12: Support Repeatable Evaluation

The system shall support repeatable tests of query validity, safety, and result correctness.

**Acceptance criteria:**
- Evaluation questions and expected outcomes are maintained in a version-controlled test set.
- Results can be reproduced under documented conditions.
- Validity, execution success, and semantic correctness are measured separately.

## 4. Non-Functional Requirements

### NFR-01: Security

The application shall restrict database access to authorized read-only operations and protect credentials.

**Target:** Zero unauthorized write operations executed during the defined security test suite.

### NFR-02: Correctness

The system shall return results consistent with the intended meaning of supported user questions.

**Target:** At least 85% expected-result correctness on the defined evaluation set.

### NFR-03: Reliability

The application shall handle anticipated dependency failures without uncontrolled application crashes.

**Verification:** Automated tests for invalid inputs, model failures, database errors, and timeouts.

### NFR-04: Performance

The application shall respond within an acceptable time for the intended use.

**Initial target:** 95th-percentile end-to-end latency below five seconds under a documented test environment.

This target will be reviewed against measured model and database performance.

### NFR-05: Maintainability

The implementation shall separate query generation, validation, database access, API handling, and result processing into understandable components.

### NFR-06: Testability

Core backend components shall support automated unit and integration testing.

**Target:** At least 80% line coverage for the defined core backend scope.

### NFR-07: Usability

The user interface shall provide understandable input guidance, result presentation, empty states, and error messages.

### NFR-08: Reproducibility

The repository shall contain setup instructions, example configuration, test procedures, and evaluation documentation sufficient for a developer to reproduce the documented workflow.

## 5. External Interface Requirements

### 5.1 User Interface

The frontend shall provide a natural-language input field, a search action, result presentation, and understandable error states.

### 5.2 LLM Interface

The backend shall send the user's question and relevant schema context to the configured LLM and receive a candidate query.

### 5.3 Database Interface

The backend shall communicate with Neo4j using an appropriate driver, parameterized queries where applicable, and restricted database permissions.

### 5.4 Configuration Interface

Database credentials, model configuration, and other environment-specific values shall be supplied through environment configuration rather than hard-coded source values.

## 6. Data Requirements

The initial datasets are:

- `meta_All_Beauty.jsonl.gz`: product metadata.
- `All_Beauty_Processed.csv`: user-product interactions and ratings.

The data pipeline shall validate identifiers, field types, duplicate records, missing attributes, and the relationship between product metadata and interactions.

The graph schema shall be documented separately in the system-design section.

## 7. Constraints

1. The initial release is limited to product discovery.
2. Results depend on the available dataset and its field completeness.
3. Query generation depends on the configured LLM.
4. Query execution depends on Neo4j availability and permissions.
5. The existing synthetic-data prototype must remain separate from the new implementation.
6. Evaluation results must be based on actual tests rather than assumed performance.

## 8. Verification and Acceptance

Each functional requirement shall be linked to one or more test cases in the Requirements Traceability Matrix.

The initial release will be evaluated using:
- Unit tests.
- Integration tests.
- Security tests.
- End-to-end product discovery tests.
- A fixed semantic-correctness evaluation set.
- Performance measurements under documented conditions.

The release shall not be considered complete solely because generated Cypher queries execute successfully.

## 9. Requirements Change Management

Changes to the approved scope or requirements shall be recorded in the project backlog, reviewed for impact, and reflected in the SRS, design documents, and test cases as appropriate.

## 10. Document Status

This SRS is a working draft. Requirements and acceptance criteria must be reviewed before the document is baselined for implementation.

**Next related artifact:** AL-RTM-001 — Requirements Traceability Matrix.