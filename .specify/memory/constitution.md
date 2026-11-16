<!--
Sync Impact Report
- Version change: unversioned scaffold -> 1.0.0
- Modified principles: placeholder scaffold -> I. Modular Domain Architecture; II. Explicit,
  Validated API Contracts; III. Durable and Consistent Persistence; IV. Reliable Asynchronous
  Processing; V. Quality, Security, and Operability by Default
- Added sections: Technology, Infrastructure, and Operational Constraints; Development Workflow
  and Quality Gates
- Removed sections: none
- Follow-up TODOs: RATIFICATION_DATE is unknown and requires confirmation by the project owner.
-->
# Polis Constitution

## Core Principles

### I. Modular Domain Architecture
Polis MUST be a Modular Monolith organized by business domain. Each module MUST own a clear
responsibility, expose only deliberate contracts, and avoid reaching into another module's internal
implementation. Business rules MUST remain independent from HTTP, database, queue, and cache
details. NestJS native dependency injection MUST be used at infrastructure boundaries. Clean
Architecture and SOLID practices MUST be applied when they create a material separation of
responsibilities; speculative layers, interfaces, classes, and services are prohibited.

Rationale: bounded modules preserve evolvability without paying the operational cost of premature
microservices or needless indirection.

### II. Explicit, Validated API Contracts
Every external input--path parameters, query strings, request bodies, headers when relevant, and
queue payloads--MUST be treated as untrusted and validated with Zod before use. API input and
output contracts MUST be explicit, versioned when a breaking change is necessary, documented in
OpenAPI, and represented with consistent response and error formats. Exception handling MUST be
centralized; errors returned to clients MUST NOT expose internal implementation details.

Authentication and authorization MUST be centralized and enforce least privilege. Sensitive routes
MUST be protected, rate limited, and designed against abuse, spam, automated actions, and
manipulation of urban-problem reports. Secrets MUST be supplied through centrally managed
environment configuration and MUST NEVER be committed or embedded in source code.

Rationale: Polis accepts civic reports and community actions, so trustworthy data and safe,
predictable APIs are core product obligations.

### III. Durable and Consistent Persistence
Drizzle ORM MUST be the sole application ORM/query builder for database access. Schemas and
versioned, reproducible migrations MUST be centralized; production database changes MUST flow
only through migrations. The database MUST enforce referential integrity with appropriate
constraints. Operations involving multiple dependent changes MUST use transactions, and critical
flows MUST account for consistency, concurrency, and recovery from partial failure.

Queries MUST be designed from expected access patterns: add justified indexes, avoid N+1 queries,
and select only needed data. Persistence models and domain entities MUST be separated only where
that boundary removes a real coupling or preserves domain clarity.

Rationale: a civic record must remain reliable, traceable, and performant as reports and community
participation grow.

### IV. Reliable Asynchronous Processing
BullMQ MUST handle background work and Redis MUST provide queue and temporary-cache
infrastructure. Long-running or failure-prone work MUST NOT execute in the HTTP request cycle.
Jobs that can run more than once MUST be idempotent and MUST define retries with appropriate
backoff. Workers MUST record failures with actionable context; critical jobs MUST be monitorable
and capable of safe reprocessing.

Redis, queues, and workers MUST be initialized, health-checked, and shut down gracefully. Cache
entries MUST have intentional ownership and expiration; cache state MUST NOT become the sole
authoritative source for durable civic data.

Rationale: asynchronous work improves responsiveness only when duplicate execution, dependency
outages, and recovery are handled deliberately.

### V. Quality, Security, and Operability by Default
The backend MUST use TypeScript in strict mode and ESM. `any` is prohibited except for a
narrowly documented, unavoidable interoperability boundary. Code MUST favor small, cohesive
functions, clear names without needless abbreviations, shared logic over duplication, and the
simplest design that fulfills current requirements without blocking foreseeable evolution.

OWASP-aligned controls, input sanitization where context requires it, secure secret management,
and protection against known vulnerabilities are mandatory. Structured logs and correlation IDs
MUST make requests and background jobs traceable. Operational errors MUST be distinguished from
programming defects, and failures of external dependencies, queues, workers, and Redis MUST be
observable.

Rationale: code quality, security, and observability are inseparable from operating a dependable
public-facing civic platform.

## Technology, Infrastructure, and Operational Constraints

The backend MUST use Node.js 26, NestJS with the Fastify HTTP adapter, pnpm, Drizzle ORM, Zod,
Docker, Redis, and BullMQ. pnpm MUST be used for every dependency-management operation. A
replacement or additional technology that overlaps one of these responsibilities MUST NOT be
introduced unless an ADR documents the technical need, alternatives considered, operational
impact, migration path, and explicit approval.

Docker MUST provide reproducible development and production environments. Docker Compose MUST
support local development dependencies. Configuration MUST be centralized and separated by
environment; documented environment variables MUST identify purpose, required status, safe
defaults where applicable, and secret handling. The application MUST implement health checks,
structured logging, graceful shutdown, and monitoring hooks for Redis, queues, workers, and other
external dependencies.

Architectural decisions that introduce material complexity, new dependencies, new cross-module
coupling, or new operational infrastructure MUST be recorded as ADRs before implementation.
Microservices MUST NOT be adopted without demonstrated module-boundary, scale, reliability, or
independent-deployment requirements that the Modular Monolith cannot meet.

## Development Workflow and Quality Gates

Specifications, plans, tasks, implementation reviews, and pull requests MUST verify compliance
with this Constitution. New work MUST state its affected domain modules, validation boundaries,
persistence and migration impact, authorization implications, asynchronous behavior, operational
signals, and test strategy when applicable. No feature, abstraction, dependency, pattern, or
infrastructure component may be added without a concrete requirement and proportional
justification.

Business rules MUST have unit tests. Persistence and communication across modules MUST have
integration tests. Critical user and operational paths MUST have end-to-end tests. Tests MUST
prioritize behavior and contracts over incidental implementation details. Coverage percentage alone
MUST NOT be used as proof of quality.

An implementation is complete only when it satisfies functional and non-functional requirements,
respects module boundaries, validates inputs, handles errors safely, has tests proportionate to its
criticality, introduces no known vulnerability, and is documented where needed. Before completion,
linting, type checking, and the applicable automated test suite MUST pass.

## Governance

This Constitution supersedes conflicting development conventions for the Polis backend. Every
specification, implementation plan, task list, review, and release assessment MUST evaluate
compliance. A non-compliant decision requires an explicit, time-bounded exception documented in
an ADR with its rationale, risks, owner, remediation or review date, and approval.

Amendments MUST be proposed in writing, assess their impact on existing specifications, plans,
templates, and code, and update this document with a Sync Impact Report during review. The report
is temporary review material and MUST be removed before the amended document is committed.
Amendments take effect only after approval by the project maintainers. Guidance documents and
development artifacts MAY add detail but MUST NOT weaken these rules.

Constitution versions follow semantic versioning: MAJOR for backward-incompatible governance
changes or principle removal/redefinition; MINOR for new principles or materially expanded
requirements; PATCH for clarifications, wording, or non-semantic corrections. Compliance reviews
MUST occur when creating or changing specifications and plans, during code review, and before a
release. The ratification date preserves the original adoption date; the last-amended date records
the most recent substantive change.

**Version**: 1.0.0 | **Ratified**: TODO(RATIFICATION_DATE): original adoption date unknown |
**Last Amended**: 2026-11-15
