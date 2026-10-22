# AI Movie Studio — Reconstruction Checklist

This checklist defines the phased milestones for reconstructing the AI Movie Studio platform following the loss of original source code.

Each milestone requires comprehensive verification across unit/integration tests, linting, type-checking, database migrations, and provenance validation before moving to subsequent phases.

**Status Legend:**
- `NOT STARTED`: Task is queued for reconstruction.
- `IN PROGRESS`: Work is actively underway.
- `VERIFIED`: Reconstructed, tested, linted, type-checked, and committed.
- `BLOCKED`: A technical blocker or gate condition prevents execution.

---

## Reconstruction Milestones Overview

| Milestone | Scope / Domain | Status | Dependencies | Gate Criteria |
| :--- | :--- | :--- | :--- | :--- |
| **M0** | Documentation & Recovery Baseline | `VERIFIED` | None | Blueprint, Checklist, and Recovery Log documented and reviewed. |
| **M1** | Project Scaffolding & Foundational Core (Phases 1–18) | `NOT STARTED` | M0 Approval | Tooling configured (ruff, mypy, pytest), core schemas, database migrations, config system. |
| **M2** | Durable Worker Engine & Job Management (Phase 19) | `NOT STARTED` | M1 | Background worker execution, leases, idempotency, retry governance. |
| **M3** | Canonical Character Identity Authority (Phase 20) | `NOT STARTED` | M1 | Immutable CharacterVersions, visual reference assets (manual + AI), consistency fingerprints. |
| **M4** | Generation Context & Lineage Manifests (Phase 21) | `NOT STARTED` | M2, M3 | Immutable GenerationManifest builder, strict version locking, worker manifest enforcement. |
| **M5** | Storage Abstractions & Provider Neutrality (Phase 22) | `NOT STARTED` | M1, M4 | Provider interface, storage backends, SHA-256 validation, SceneOutputRegistry provenance. |
| **M6** | Production Hierarchy & Shot Orchestration (Phase 23) | `NOT STARTED` | M1, M4 | Movie -> Act -> Scene -> Shot models, dependency DAG resolution, batch scheduling. |
| **M7** | Assembly Timeline & FFmpeg Render Engine (Phase 24 & 24A) | `NOT STARTED` | M5, M6 | Timeline specification, FFmpeg local runner, safe temp staging, ffprobe media validation. |
| **M8** | Quality Evaluation & Director Review (Phase 25) | `NOT STARTED` | M5, M7 | QC gates, visual consistency review, non-destructive regeneration branching. |
| **M9** | Movie Lifecycle & Release Governance (Phase 26) | `NOT STARTED` | M7, M8 | Lifecycle gate enforcement, immutable ReleaseManifest, packaging and archival. |
| **M10** | Creative Development & Screenplay Handoff (Phase 27) | `NOT STARTED` | M6 | Screenplay authoring, dramatic beats, storyboard breakdown, planning handoff. |
| **M11** | Operational Observability & Telemetry (Phase 28) | `NOT STARTED` | M2, M5, M7 | Event bus, read-only projection listeners, metrics, bottleneck analysis. |
| **M12** | Cloud Provider Integration (Google Veo Adapter) | `NOT STARTED` | M2, M5 | Google Veo 3.1 Fast provider adapter, polling, rate limiting, error handling. |
| **M13** | Physical Pilot 01 Execution & Verification | `NOT STARTED` | M7, M12 | Two real Veo shots with identical character, FFmpeg assembly to `pilot.mp4`, full provenance audit. |

---

## Detailed Milestone Task Breakdown

### Milestone M0: Recovery Baseline & Documentation
- [x] Document Recovery Blueprint (`docs/RECOVERY_BLUEPRINT.md`) — `VERIFIED`
- [x] Document Reconstruction Checklist (`docs/RECONSTRUCTION_CHECKLIST.md`) — `VERIFIED`
- [x] Document Recovery Log (`docs/RECOVERY_LOG.md`) — `VERIFIED`
- [x] Verify Git history, branch cleanliness, and remote safety — `VERIFIED`
- [ ] Await explicit user authorization before initiating code implementation — `IN PROGRESS`

### Milestone M1: Project Scaffolding & Core Architecture (Phases 1–18)
- [ ] Verify Python 3.12 baseline and dependency matrix compatibility — `NOT STARTED`
- [ ] Set up Python project configuration (`pyproject.toml`, virtual environment specifications) — `NOT STARTED`
- [ ] Configure Ruff (linting/formatting), Mypy (strict type-checking), and Pytest — `NOT STARTED`
- [ ] Create Docker Compose configuration for local PostgreSQL service (`docker-compose.yml`) — `NOT STARTED`
- [ ] Reconstruct base configuration system (environment settings, provider configuration schemas, secrets isolation) — `NOT STARTED`
- [ ] Implement foundational database models and migrations with SQLAlchemy and Alembic (PostgreSQL canonical) — `NOT STARTED`
- [ ] Implement core shared utilities (logging, hashing, UUID generation, datetime utilities) — `NOT STARTED`
- [ ] **Verification Gate**:
  - `pytest` passes with 100% of tests green
  - `mypy --strict` passes with zero errors
  - `ruff check` and `ruff format --check` pass
  - PostgreSQL schema migrations apply cleanly via Alembic
  - Git checkpoint commit & push

### Milestone M2: Durable Worker Engine & Job Management (Phase 19)
- [ ] Implement `GenerationJob` and task state machine (`PENDING`, `ACQUIRED`, `RUNNING`, `COMPLETED`, `FAILED`, `CANCELLED`) — `NOT STARTED`
- [ ] Implement PostgreSQL-backed durable queue worker (`SELECT ... FOR UPDATE SKIP LOCKED`) without Redis/Celery — `NOT STARTED`
- [ ] Implement lease management mechanism with automatic heartbeat renewals and timeout re-queuing — `NOT STARTED`
- [ ] Implement idempotency key constraints preventing duplicate job dispatch — `NOT STARTED`
- [ ] Implement exponential backoff retry governance with dead-letter queue handling and interrupted-worker recovery — `NOT STARTED`
- [ ] **Verification Gate**:
  - Unit & concurrent integration tests for lease expiration, worker recovery, and idempotent duplicate handling
  - Static type-checking and linting verification
  - Git checkpoint commit & push

### Milestone M3: Canonical Character Identity Authority (Phase 20)
- [ ] Implement `Character` canonical profile entity — `NOT STARTED`
- [ ] Implement immutable `CharacterVersion` model and database persistence — `NOT STARTED`
- [ ] Support reference asset attachments (AI-generated turnaround sheets and manual user-provided reference images) — `NOT STARTED`
- [ ] Implement character consistency fingerprint calculation and reference hashing — `NOT STARTED`
- [ ] Implement scene-character assignment binding logic (ensuring identity is decoupled from scene styling) — `NOT STARTED`
- [ ] **Verification Gate**:
  - Immutability tests (assert mutations raise exceptions on locked versions)
  - Unit tests for dual reference asset ingestion and fingerprinting
  - Git checkpoint commit & push

### Milestone M4: Generation Manifests & Lineage (Phase 21)
- [ ] Implement `GenerationManifest` builder locking prompt, camera directions, lighting, seed, dimensions, and exact `character_version_id` — `NOT STARTED`
- [ ] Implement manifest validation rules enforcing strict immutability once locked — `NOT STARTED`
- [ ] Implement worker dispatch validation ensuring workers only execute against locked manifests and cannot query "latest" character records — `NOT STARTED`
- [ ] **Verification Gate**:
  - Tests ensuring workers reject manifests lacking explicit locked version IDs
  - Strict serialization and deserialization integrity tests
  - Git checkpoint commit & push

### Milestone M5: Storage Abstractions & SceneOutputRegistry (Phase 22)
- [ ] Implement abstract `StorageProvider` interface with local filesystem and cloud storage (e.g. S3/GCS) backends — `NOT STARTED`
- [ ] Implement `SceneOutputRegistry` tracking output URI, SHA-256 checksum, MIME type, dimensions, duration, provider, seed, and lineage pointers — `NOT STARTED`
- [ ] Implement provider adapter base interface (`VideoGenerationProvider`, `ImageGenerationProvider`) ensuring vendor SDK isolation — `NOT STARTED`
- [ ] Implement SHA-256 automated computation and verification during asset ingestion — `NOT STARTED`
- [ ] **Verification Gate**:
  - Round-trip storage tests with checksum verification
  - Provider isolation tests confirming no vendor SDK leakage into domain models
  - Git checkpoint commit & push

### Milestone M6: Production Hierarchy & Shot Orchestration (Phase 23)
- [ ] Implement production hierarchy data models: `Movie`, `Act`, `Scene`, `Shot` — `NOT STARTED`
- [ ] Implement shot dependency DAG tracking (e.g. background matte dependencies, character plate sequences) — `NOT STARTED`
- [ ] Implement production scheduler resolving runnable shot manifests based on dependency fulfillment — `NOT STARTED`
- [ ] **Verification Gate**:
  - DAG cycle detection tests and topological sorting validation
  - Production tree traversal and status roll-up tests
  - Git checkpoint commit & push

### Milestone M7: Assembly Timeline & Local FFmpeg Render Engine (Phase 24 & 24A)
- [ ] Implement `AssemblyManifest` builder supporting multi-track timelines (video, dialogue, audio) binding exact `scene_output_id`s — `NOT STARTED`
- [ ] Implement system FFmpeg/FFprobe discovery and capability checking — `NOT STARTED`
- [ ] Implement FFmpeg render command generator with filter-graph assembly and concatenation — `NOT STARTED`
- [ ] Implement safe isolated temporary rendering (`temp_render_<uuid>.mp4`) with atomic rename/move upon success — `NOT STARTED`
- [ ] Implement `ffprobe` decodability, duration, frame rate, and integrity validation on rendered outputs — `NOT STARTED`
- [ ] Compute and register rendered media into `SceneOutputRegistry` — `NOT STARTED`
- [ ] **Verification Gate**:
  - Unit tests for timeline assembly math and manifest compilation
  - Integration tests with real local FFmpeg rendering synthetic test patterns/audio tones
  - Decodability and checksum verification test suite
  - Git checkpoint commit & push

### Milestone M8: Quality Evaluation & Director Review (Phase 25)
- [ ] Implement quality evaluation models: technical diagnostic scores (video bitrates, audio sync) and visual review ratings — `NOT STARTED`
- [ ] Implement director review workflow (`PENDING_REVIEW`, `APPROVED`, `REVISE_REQUESTED`, `REJECTED`) — `NOT STARTED`
- [ ] Implement governed regeneration logic ensuring new manifests and lineage nodes are created without mutating existing history — `NOT STARTED`
- [ ] **Verification Gate**:
  - Verification that rejections or re-generation requests do not delete or alter historical outputs
  - State machine tests for review approvals
  - Git checkpoint commit & push

### Milestone M9: Movie Lifecycle & Release Governance (Phase 26)
- [ ] Implement movie lifecycle state machine (`DRAFT` -> `IN_PRODUCTION` -> `IN_ASSEMBLY` -> `REVIEW` -> `RELEASE_CANDIDATE` -> `RELEASED` -> `ARCHIVED`) — `NOT STARTED`
- [ ] Ensure `BLOCKED` is handled strictly as an operational condition flag, not a lifecycle state — `NOT STARTED`
- [ ] Implement immutable `ReleaseManifest` capturing full production pedigree, checksums of all constituent scenes, and final master media — `NOT STARTED`
- [ ] Implement distribution packaging and release verification bundle generator — `NOT STARTED`
- [ ] **Verification Gate**:
  - Lifecycle gate enforcement tests (cannot transition to RELEASED without signed QC approvals and verified checksums)
  - Release package verification tests
  - Git checkpoint commit & push

### Milestone M10: Creative Development & Screenplay Handoff (Phase 27)
- [ ] Implement creative domain entities: `Screenplay`, `SceneCard`, `DramaticBeat`, `StoryboardItem` — `NOT STARTED`
- [ ] Implement screenplay import/export and breakdown parser — `NOT STARTED`
- [ ] Implement handoff bridge converting creative scene cards and storyboard items into Phase 23 planned production shots — `NOT STARTED`
- [ ] **Verification Gate**:
  - Screenplay parsing tests
  - Handoff transformation tests validating that creative intent maps accurately into shot specs
  - Git checkpoint commit & push

### Milestone M11: Operational Observability & Telemetry (Phase 28)
- [ ] Implement non-intrusive domain event bus (`JobStartedEvent`, `ShotRenderedEvent`, `QualityEvaluatedEvent`, etc.) — `NOT STARTED`
- [ ] Implement read-only projection tables and aggregators (production velocity, GPU/cloud compute costs, worker throughput) — `NOT STARTED`
- [ ] Implement bottleneck detector and operational alerting rules — `NOT STARTED`
- [ ] Enforce architectural invariant: Phase 28 remains strictly read-only and exerts zero business-control logic — `NOT STARTED`
- [ ] **Verification Gate**:
  - Event listener decoupled execution tests
  - Assertion tests verifying that disabling Phase 28 causes zero failures in core production flows
  - Git checkpoint commit & push

### Milestone M12: Cloud Provider Integration (Google Veo Adapter)
- [ ] Implement Google Veo 3.1 Fast provider adapter encapsulating official `google-genai` SDK adhering to Phase 22 standard provider interface — `NOT STARTED`
- [ ] Implement asynchronous polling, rate-limiting, timeout, and transient error retry logic — `NOT STARTED`
- [ ] Implement credential resolution via secure environment configuration (no hardcoded keys) — `NOT STARTED`
- [ ] Enforce ADR-02 guardrail: real, billable provider calls are strictly opt-in and prohibited during scaffolding/testing — `NOT STARTED`
- [ ] **Verification Gate**:
  - Mocked provider contract test suite validating request payload formatting and response parsing without making network/billable calls
  - Git checkpoint commit & push

### Milestone M13: Physical Pilot 01 Execution & Verification
- [ ] Secure physical pilot authorization and valid local API configuration — `NOT STARTED`
- [ ] Run canonical character creation and lock `CharacterVersion` with reference imagery — `NOT STARTED`
- [ ] Lock `GenerationManifest` for Shot 01 and Shot 02 with identical character reference context — `NOT STARTED`
- [ ] Dispatch generation jobs to real Google Veo 3.1 Fast cloud endpoint and await completion — `NOT STARTED`
- [ ] Ingest generated MP4s into storage and register in `SceneOutputRegistry` with SHA-256 hashes — `NOT STARTED`
- [ ] Construct Phase 24 `AssemblyManifest` sequencing the two shots — `NOT STARTED`
- [ ] Render playable `pilot.mp4` via Phase 24A local FFmpeg engine — `NOT STARTED`
- [ ] Execute `ffprobe` decodability check, verify exact combined duration, and calculate final SHA-256 checksum — `NOT STARTED`
- [ ] **Verification Gate**:
  - Successful playback verification of `pilot.mp4`
  - Complete, un-broken provenance report from character version to final file
  - Post-pilot audit review and user sign-off
