# AgentLens — Project Charter

**Document ID:** AL-DOC-001  
**Version:** 1.0  
**Status:** Approved scope  
**Date:** 13 October 2026

## 1. Project Overview

**Project Name:** AgentLens

**Project Title:** Text-to-Cypher Assistant for E-Commerce Product Discovery

**Project Domain:** Artificial Intelligence, Large Language Models, Knowledge Graphs, and Natural Language Processing.

AgentLens is a software application that enables users to search an e-commerce product knowledge graph using natural-language questions. It translates user requests into Cypher queries, validates them, executes permitted read-only queries against Neo4j, and presents the results in an understandable format.

## 2. Problem Statement

E-commerce product catalogs contain structured information about products, brands, ratings, prices, and other attributes. Retrieving relevant information from a graph database typically requires knowledge of a query language such as Cypher.

Users without database-querying expertise may find this process difficult.

AgentLens aims to reduce this barrier by providing a natural-language interface to a Neo4j product knowledge graph.

The primary technical challenge is ensuring that generated queries are safe, consistent with the graph schema, and semantically correct for the user's request.

## 3. Project Objectives

The project aims to:

1. Build a Neo4j knowledge graph using real e-commerce product data.
2. Support natural-language product discovery.
3. Generate schema-aware Cypher queries using a suitable LLM.
4. Validate queries before database execution.
5. Restrict execution to authorized read-only operations.
6. Present matching products and handle errors clearly.
7. Evaluate query validity, execution success, semantic correctness, and performance.
8. Apply Agile development practices throughout the project lifecycle.
9. Maintain version-controlled requirements, diagrams, test evidence, and technical documentation.

## 4. Project Scope

### 4.1 In Scope

- Natural-language product discovery.
- Product-name and brand searches where data is available.
- Rating and price filtering where valid attribute values exist.
- Schema-aware Cypher generation.
- Query validation and read-only execution controls.
- Neo4j graph integration.
- Result presentation and error handling.
- Automated unit, integration, and evaluation tests.
- Agile planning, requirements traceability, and technical documentation.

### 4.2 Out of Scope

The following are excluded from the initial release:

- Purchasing, payments, and order management.
- Live Amazon inventory or price synchronization.
- Medical or dermatological recommendations.
- Training a foundation model from scratch.
- Production-scale multi-tenant deployment.
- Product comparison and conversational follow-up functionality.

Additional features may be considered after the initial release has been evaluated.

## 5. Dataset Overview

The initial real-data implementation will use:

**Product metadata**
- File: `meta_All_Beauty.jsonl.gz`
- Initial observed records: 112,590
- Contains product identifiers, titles, ratings, brands or stores, prices, and other metadata fields where available.

**User-product interactions**
- File: `All_Beauty_Processed.csv`
- Initial observed records: 2,029
- Contains user identifiers, product identifiers, user ratings, timestamps, and interaction history.

Initial validation identified 341 distinct interaction products, all of which matched product metadata records.

These figures represent the initial data inspection and must be revalidated in the new implementation.

Raw datasets will be kept outside the public repository unless their licensing and redistribution conditions permit publication.

## 6. Proposed Technology Stack

| Component | Proposed technology |
|---|---|
| Programming language | Python |
| API backend | FastAPI |
| Graph database | Neo4j Aura |
| Query generation | Suitable LLM selected through evaluation |
| Data processing | Pandas |
| Testing | pytest |
| Frontend | React |
| Version control | Git and GitHub |
| Diagrams | Mermaid and diagrams.net |
| Project management | GitHub Issues and Projects |

Technology choices may be revised through documented design decisions if testing demonstrates a clear benefit.

## 7. Development Methodology

The project will use an Agile Scrum-inspired workflow with one-week sprints.

Each sprint will include:

1. Sprint planning and task prioritization.
2. Implementation of selected backlog items.
3. Testing and documentation updates.
4. Sprint review and demonstration.
5. Retrospective and improvement actions.

GitHub will be used to manage source code, issues, project tasks, documentation, and change history.

## 8. Success Criteria

The initial proposed targets are:

| Metric | Target |
|---|---|
| Valid and safe query execution | At least 95% of defined test cases |
| Expected-result correctness | At least 85% of defined evaluation cases |
| Unauthorized write operations executed | Zero |
| Core backend line coverage | At least 80% |
| Critical project documentation | Complete and version-controlled |

These are target values, not achieved results. The evaluation dataset, test methodology, and measurement conditions must be documented before assessing the final release.

## 9. Key Risks

| Risk | Mitigation |
|---|---|
| Missing or inconsistent product attributes | Validate data types, measure field completeness, and handle missing values explicitly. |
| Incorrect Cypher generation | Supply schema context and test semantic correctness. |
| Unsafe generated queries | Enforce read-only database permissions and query validation. |
| Model or database failures | Implement controlled error handling and diagnostic logging. |
| Excessive scope expansion | Prioritize the initial release and manage additions through the product backlog. |
| Unreproducible results | Version-control code, test cases, configuration examples, and evaluation procedures. |

## 10. Project Deliverables

- Project Charter.
- Software Requirements Specification.
- Use case specifications and diagrams.
- Requirements Traceability Matrix.
- Architecture and database design documentation.
- Activity, sequence, component, deployment, and data-flow diagrams.
- Product backlog and sprint records.
- Working application and automated test suite.
- Evaluation report with measured results.
- Installation guide and user documentation.
- Final technical report and demonstration.

## 11. Project Baseline and Version Control

The existing synthetic-data prototype will be preserved separately as Version 1.

The new real-data implementation will be developed independently, with separate configuration and a separate Neo4j database or instance as supported by the selected plan.

Changes to scope, requirements, or architecture will be documented and reviewed rather than introduced without traceability.

## 12. Approval

The initial project direction and scope have been agreed upon by the project owner.

Detailed requirements, architecture, implementation, and evaluation remain subject to their respective review and completion stages.

**Project status:** Initiation complete; requirements engineering in progress.