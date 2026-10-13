# AgentLens — Use Case Specifications

**Document ID:** AL-UC-001  
**Version:** 1.0  
**Status:** Draft  
**Parent Document:** AL-SRS-001 — Software Requirements Specification  
**Related Document:** AL-RTM-001 — Requirements Traceability Matrix

## 1. Purpose

This document specifies the interactions between AgentLens and its users or external systems. It defines the actors, use cases, preconditions, main flows, alternative flows, and postconditions for the initial release.

The specifications serve as the basis for the Use Case Diagram, Activity Diagram, Sequence Diagram, and functional acceptance tests.

## 2. Actors

### A1 — End User

The person who submits natural-language product discovery requests and views matching products.

### A2 — Developer / Administrator

The person responsible for configuring the application, maintaining its dependencies, and investigating failures.

### A3 — LLM Service

An external model service that generates candidate Cypher queries using the user's request and relevant graph-schema context.

### A4 — Neo4j Database

The external graph database that stores product information and executes permitted read-only queries.

**Note:** The query validator, query orchestrator, and result processor are internal system components, not external actors.

## 3. Use Case Summary

| Use Case ID | Name | Primary Actor |
|---|---|---|
| UC-01 | Search Products | End User |
| UC-02 | Generate Cypher Query | AgentLens, interacting with the LLM Service |
| UC-03 | Validate Query | AgentLens |
| UC-04 | Execute Read-Only Query | AgentLens, interacting with Neo4j |
| UC-05 | View Search Results | End User |
| UC-06 | Handle Processing Errors | AgentLens |
| UC-07 | Configure Application | Developer / Administrator |
| UC-08 | Diagnose Failures | Developer / Administrator |

## 4. Detailed Use Cases

### UC-01 — Search Products

**Primary actor:** End User

**Goal:** Retrieve products that match a natural-language request.

**Preconditions:**
- The application is available.
- The product graph has been initialized.
- Required application dependencies are configured.

**Trigger:** The user submits a product discovery question.

**Main flow:**
1. The user enters a natural-language question.
2. AgentLens validates the input.
3. The system retrieves the relevant graph-schema context.
4. The query-generation component requests a candidate Cypher query from the LLM Service.
5. The query validator checks the candidate.
6. If validation succeeds, the query executor runs the permitted read-only query against Neo4j.
7. The system processes the returned records.
8. AgentLens presents the matching products to the user.

**Alternative flows:**
- If the input is empty, the system asks the user to enter a question.
- If no products match, the system displays an empty-result message.
- If the request cannot be supported by the available schema or data, the system explains the limitation.
- If a correctable query-generation or validation failure occurs, the system may attempt a bounded regeneration; otherwise, it returns a controlled error.

**Postconditions:**
- Matching results or a controlled response are returned.
- No unauthorized database operation is executed.

**Related requirements:** FR-01 to FR-10.

### UC-02 — Generate Cypher Query

**Primary actor:** AgentLens query-generation component

**Supporting actor:** LLM Service

**Goal:** Convert a natural-language request into a candidate Cypher query.

**Preconditions:**
- The input has passed basic validation.
- Relevant graph-schema context is available.

**Trigger:** The query orchestrator initiates query generation.

**Main flow:**
1. The system prepares the user's question and relevant schema context.
2. The system sends the generation request to the LLM Service.
3. The LLM Service returns a candidate Cypher query.
4. The system extracts the query and associated generation metadata, if available.
5. The candidate is passed to the query validator.

**Alternative flows:**
- If the response does not contain a usable query, the system records a generation failure.
- If the model service is unavailable, the system returns a controlled error.
- If regeneration is permitted, the system may retry within a configured limit.

**Postconditions:**
- A candidate query is available for validation, or a generation failure is reported.
- The generated query has not been executed merely because the model returned it.

**Related requirements:** FR-02, FR-03, FR-10.

### UC-03 — Validate Query

**Primary actor:** AgentLens query validator

**Goal:** Determine whether a candidate query is permitted to execute.

**Preconditions:**
- A candidate Cypher query is available.

**Trigger:** The query orchestrator submits the candidate for validation.

**Main flow:**
1. The validator checks the query's structure and permitted query form.
2. The validator checks references against the supported graph schema.
3. The validator checks parameter handling and configured execution limits.
4. The validator checks read-only restrictions.
5. If all required checks pass, the validator marks the query as permitted for execution.
6. Otherwise, the validator rejects the query and records the failure reason.

**Alternative flows:**
- A correctable generation error may trigger a bounded regeneration attempt.
- A prohibited operation is rejected and must not be forwarded for execution.

**Postconditions:**
- The query is either permitted for execution or rejected.
- Rejected queries cannot bypass the validation gate.

**Related requirements:** FR-04, FR-05.

### UC-04 — Execute Read-Only Query

**Primary actor:** AgentLens query executor

**Supporting actor:** Neo4j Database

**Goal:** Retrieve records using a validated query.

**Preconditions:**
- The query has passed validation.
- The database connection is available.
- The configured database credentials have appropriate restricted permissions.

**Trigger:** The query orchestrator requests query execution.

**Main flow:**
1. The executor receives the validated query and parameters.
2. The executor applies configured timeouts and result limits.
3. The query is executed against Neo4j.
4. The database returns records or an execution status.
5. The executor returns structured results to the result processor.

**Alternative flows:**
- If no records match, an empty result set is returned.
- If a query times out or the connection fails, the executor reports a controlled error.
- If Neo4j rejects the query, the error is handled without exposing sensitive connection information.

**Postconditions:**
- Structured results or a controlled execution failure are returned.
- No unauthorized write operation is performed.

**Related requirements:** FR-05, FR-06, FR-10.

### UC-05 — View Search Results

**Primary actor:** End User

**Goal:** Understand the products returned by a search.

**Preconditions:**
- The search workflow has produced a result set or an empty-result status.

**Trigger:** The result processor returns the search outcome.

**Main flow:**
1. The frontend receives the structured response.
2. The frontend displays matching products and available attributes.
3. The user reviews the results.

**Alternative flows:**
- If no products match, the frontend displays a clear empty state.
- If a search fails, the frontend displays an understandable error message.

**Postconditions:**
- The user can distinguish successful results, empty results, and failures.

**Related requirements:** FR-07, FR-08, FR-09, FR-10.

### UC-06 — Handle Processing Errors

**Primary actor:** AgentLens

**Goal:** Handle failures safely and consistently.

**Preconditions:**
- A processing, validation, model, or database failure has occurred.

**Trigger:** A component reports an error.

**Main flow:**
1. The system identifies the stage at which the failure occurred.
2. The system classifies the error.
3. The system records appropriate diagnostic information.
4. The system removes sensitive details from user-facing error messages.
5. The system returns a controlled response.

**Alternative flows:**
- If a retry is appropriate, the system retries within configured limits.
- If recovery is not possible, the request terminates with a controlled failure.

**Postconditions:**
- The user receives an understandable response.
- Relevant diagnostics are available without unnecessarily exposing sensitive data.

**Related requirements:** FR-10, FR-11, NFR-01, NFR-03.

### UC-07 — Configure Application

**Primary actor:** Developer / Administrator

**Goal:** Configure the application for the intended environment.

**Preconditions:**
- The developer has access to the deployment or development environment.

**Main flow:**
1. The developer configures database connection settings.
2. The developer configures the selected LLM integration.
3. The developer configures application parameters and execution limits.
4. The application validates required configuration during startup.
5. The application reports whether initialization succeeded.

**Alternative flows:**
- If required configuration is missing or invalid, startup fails safely with an actionable diagnostic.
- Credentials are never displayed in diagnostic messages.

**Postconditions:**
- The application is configured or reports a controlled initialization failure.

**Related requirements:** FR-11, NFR-01, NFR-08.

### UC-08 — Diagnose Failures

**Primary actor:** Developer / Administrator

**Goal:** Investigate application failures and identify corrective actions.

**Preconditions:**
- Diagnostic logs, test output, or evaluation records are available.

**Main flow:**
1. The developer inspects the relevant diagnostic information.
2. The developer identifies the failure stage.
3. The developer examines associated test results and application behavior.
4. The developer records the cause and corrective action where appropriate.
5. The developer reruns the relevant tests after making a change.

**Alternative flows:**
- If available diagnostics are insufficient, additional safe diagnostic logging or a reproducible test is added.

**Postconditions:**
- The failure is understood, documented, or assigned for further investigation.

**Related requirements:** FR-11, FR-12, NFR-03, NFR-08.

## 5. Use Case Relationships

UC-01 is the main user-facing workflow. It depends on query generation, validation, execution, and result presentation.

The validation stage must complete successfully before query execution begins. Error handling may be invoked from any processing stage when a failure occurs.

These relationships describe workflow dependencies. UML `<<include>>` and `<<extend>>` relationships will be assigned in the diagram only where their formal meanings are appropriate.

## 6. Traceability

| Use Case | Related Requirements |
|---|---|
| UC-01 | FR-01, FR-02, FR-03, FR-04, FR-05, FR-06, FR-07, FR-10 |
| UC-02 | FR-02, FR-03 |
| UC-03 | FR-04, FR-05 |
| UC-04 | FR-05, FR-06, FR-10 |
| UC-05 | FR-07, FR-08, FR-09, FR-10 |
| UC-06 | FR-10, FR-11, NFR-01, NFR-03 |
| UC-07 | FR-11, NFR-01, NFR-08 |
| UC-08 | FR-11, FR-12, NFR-03, NFR-08 |

## 7. Document Status

The use case specifications define the intended behavior of the initial release. They must be reviewed against the SRS and updated if the approved requirements change.

No use case should be marked implemented or verified until the corresponding functionality and tests exist.

**Next artifact:** AL-DES-001 — System Design and UML Diagrams.