---

description: "Task list template for feature implementation"
---

# Tasks: [FEATURE NAME]

**Input**: Design documents from `/specs/[###-feature-name]/`
**Prerequisites**: plan.md (required), spec.md (required for user stories),
research.md, data-model.md, contracts/

**Tests**: Tests are REQUIRED when a feature changes canonicalization,
leaf/root semantics, proof bundle formats, verifier behavior, witness or
publication assumptions, or protocol contract assumptions. They are optional
only for purely editorial or research-only work.

**Organization**: Tasks are grouped by user story to enable independent
implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- **Protocol rules and message logic**: `protocols/<protocol>/`
- **Peer-specific code**: `peers/<peer>/`
- **Witness or publication code**: `witnesses/`
- **Shared schemas**: `schemas/`
- **Verifier surfaces**: `verifiers/browser/`, `verifiers/cli/`,
  `verifiers/api/`
- **Verification fixtures**: `tests/contract/`, `tests/golden/`,
  `tests/integration/`, `tests/replay/`

<!--
  ============================================================================
  IMPORTANT: The tasks below are SAMPLE TASKS for illustration purposes only.

  The /speckit.tasks command MUST replace these with actual tasks based on:
  - User stories from spec.md (with their priorities P1, P2, P3...)
  - Feature requirements from plan.md
  - Entities from data-model.md
  - Endpoints from contracts/

  Tasks MUST be organized by user story so each story can be:
  - Implemented independently
  - Tested independently
  - Delivered as an MVP increment

  DO NOT keep these sample tasks in the generated tasks.md file.
  ============================================================================
-->

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization and basic structure

- [ ] T001 Create feature directories per implementation plan
- [ ] T002 Capture protocol references, peer examples, and local fixture
          inputs for the feature
- [ ] T003 [P] Configure formatting, linting, and fixture management scripts

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core infrastructure that MUST be complete before ANY user story
can be implemented

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

Examples of foundational tasks (adjust based on your project):

- [ ] T004 Define canonical fact, root, and proof-bundle schemas in `schemas/`
- [ ] T004a [P] Define certification schemas in `schemas/certifications/` when
          the feature promotes derived facts
- [ ] T005 [P] Implement peer canonicalization boundaries in
          `peers/<peer>/canonical/`
- [ ] T006 [P] Implement protocol message or proof assembly rules in
          `protocols/<protocol>/`
- [ ] T007 Create witness or publication model in `witnesses/` or
          `peers/<peer>/publication/`
- [ ] T008 Establish golden fixtures and replay harness in `tests/golden/` and
          `tests/replay/`
- [ ] T009 Setup verification harness in the selected surface under
          `verifiers/`

**Checkpoint**: Foundation ready - user story implementation can now begin in
parallel

---

## Phase 3: User Story 1 - [Title] (Priority: P1) 🎯 MVP

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own]

### Tests for User Story 1 ⚠️

> **NOTE: Write these tests FIRST, ensure they FAIL before implementation**

- [ ] T010 [P] [US1] Contract test for peer message or root mapping in
          `tests/contract/`
- [ ] T011 [P] [US1] Golden test proving deterministic proof-bundle
          reproduction in `tests/golden/`
- [ ] T012 [P] [US1] Integration test for the primary verification journey in
          `tests/integration/`

### Implementation for User Story 1

- [ ] T013 [P] [US1] Implement canonical mapping for the primary peer facts in
          `peers/<peer>/canonical/`
- [ ] T014 [US1] Implement proof-bundle or protocol-message assembly in
          `protocols/<protocol>/` or `witnesses/`
- [ ] T015 [US1] Implement bundle retrieval or export flow in the chosen
          surface
- [ ] T016 [US1] Implement independent partial verification for the primary
          shared claim in `verifiers/`
- [ ] T017 [US1] Add degraded-mode messaging for missing peers, stale roots,
          or incomplete proofs
- [ ] T017a [US1] If applicable, implement certification emission for derived
          facts in `peers/<peer>/certifications/`

**Checkpoint**: At this point, User Story 1 should be fully functional and
testable independently

---

## Phase 4: User Story 2 - [Title] (Priority: P2)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own]

### Tests for User Story 2 ⚠️

- [ ] T018 [P] [US2] Contract test for the additional peer or protocol scope in
          `tests/contract/`
- [ ] T019 [P] [US2] Negative verification test for discrepancy or tamper cases
          in `tests/integration/`

### Implementation for User Story 2

- [ ] T020 [P] [US2] Extend canonical schema or proof payload in `schemas/`
- [ ] T021 [US2] Implement the secondary cross-peer claim in
          `protocols/<protocol>/` or `witnesses/`
- [ ] T022 [US2] Expose verification or discrepancy output in `verifiers/`
- [ ] T023 [US2] Integrate with User Story 1 without weakening independent
          verification
- [ ] T023a [US2] If certifications are reusable, add audit-through support in
          `verifiers/` and `tests/integration/`

**Checkpoint**: At this point, User Stories 1 AND 2 should both work
independently

---

## Phase 5: User Story 3 - [Title] (Priority: P3)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own]

### Tests for User Story 3 ⚠️

- [ ] T024 [P] [US3] Replay test for historical reconstruction in
          `tests/replay/`
- [ ] T025 [P] [US3] Cross-surface verification parity test in
          `tests/integration/`

### Implementation for User Story 3

- [ ] T026 [P] [US3] Implement historical replay or audit packaging in
          `protocols/<protocol>/`, `witnesses/`, or `verifiers/`
- [ ] T027 [US3] Implement publication or sharing flow for the enhanced
          artifact
- [ ] T028 [US3] Document verification steps for external recipients in
          `docs/`

**Checkpoint**: All user stories should now be independently functional

---

[Add more user story phases as needed, following the same pattern]

---

## Phase N: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories

- [ ] TXXX [P] Documentation updates in `docs/`
- [ ] TXXX Code cleanup and refactoring
- [ ] TXXX Performance and storage optimization across all stories
- [ ] TXXX [P] Additional verifier parity or replay tests
- [ ] TXXX Security and key-custody hardening
- [ ] TXXX Run `quickstart.md` validation

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user
  stories
- **User Stories (Phase 3+)**: All depend on Foundational phase completion
  - User stories can then proceed in parallel (if staffed)
  - Or sequentially in priority order (P1 → P2 → P3)
- **Polish (Final Phase)**: Depends on all desired user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Foundational (Phase 2) - No
  dependencies on other stories
- **User Story 2 (P2)**: Can start after Foundational (Phase 2) - May
  integrate with US1 but should be independently testable
- **User Story 3 (P3)**: Can start after Foundational (Phase 2) - May
  integrate with US1/US2 but should be independently testable

### Within Each User Story

- Tests (if included) MUST be written and FAIL before implementation
- Canonical schemas before proof assembly
- Certification schemas before certification emission
- Proof assembly before verifier updates
- Core implementation before integration
- Story complete before moving to next priority

### Parallel Opportunities

- All Setup tasks marked [P] can run in parallel
- All Foundational tasks marked [P] can run in parallel (within Phase 2)
- Once Foundational phase completes, all user stories can start in parallel (if
  team capacity allows)
- All tests for a user story marked [P] can run in parallel
- Different user stories can be worked on in parallel by different team members

---

## Parallel Example: User Story 1

```bash
# Launch deterministic verification tests together:
Task: "Contract test for peer message or root mapping in tests/contract/"
Task: "Golden test proving deterministic proof-bundle reproduction in tests/golden/"
Task: "Integration test for the primary verification journey in tests/integration/"

# Launch independent canonical work together:
Task: "Implement canonical mapping for the primary peer facts"
Task: "Prepare proof-bundle retrieval flow in the chosen surface"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (CRITICAL - blocks all stories)
3. Complete Phase 3: User Story 1
4. **STOP and VALIDATE**: Verify the primary shared claim independently
5. Deploy/demo if ready

### Incremental Delivery

1. Complete Setup + Foundational → Foundation ready
2. Add User Story 1 → Test independently → Deploy/Demo (MVP!)
3. Add User Story 2 → Test independently → Deploy/Demo
4. Add User Story 3 → Test independently → Deploy/Demo
5. Each story adds value without breaking previous stories

### Parallel Team Strategy

With multiple developers:

1. Team completes Setup + Foundational together
2. Once Foundational is done:
   - Developer A: User Story 1
   - Developer B: User Story 2
   - Developer C: User Story 3
3. Stories complete and integrate independently
