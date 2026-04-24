# Feature Specification: [FEATURE NAME]

**Feature Branch**: `[###-feature-name]`  
**Created**: [DATE]  
**Status**: Draft  
**Input**: User description: "$ARGUMENTS"

## System Context *(mandatory)*

**Protocol / Domain**: [Name the protocol or workflow family]  
**Participants**: [Peers, their roles, and who verifies whom]  
**Shared Claim**: [What cross-peer statement or outcome is being evaluated]  
**Peer-Owned Facts**: [What each peer is authoritative for]  
**Derived / Certified Claim**: [If applicable, what higher-order fact a peer
may publish after verifying inputs]  
**Trust Problem**: [What ambiguity, equivocation, or over-centralization is
being reduced]  
**Primary Artifact**: [Proof bundle, checkpoint package, claim package, etc.]  
**Verification Audience**: [Who needs to verify the artifact and in what
setting]  
**Non-Goals**: [What stays outside scope, especially centralized coordinators,
or what does not need Merkle state]

## User Scenarios & Testing *(mandatory)*

<!--
  IMPORTANT: User stories should be PRIORITIZED as user journeys ordered by importance.
  Each user story/journey must be INDEPENDENTLY TESTABLE - meaning if you implement just ONE of them,
  you should still have a viable MVP (Minimum Viable Product) that delivers value.

  Assign priorities (P1, P2, P3, etc.) to each story, where P1 is the most critical.
  Think of each story as a standalone slice of functionality that can be:
  - Developed independently
  - Tested independently
  - Deployed independently
  - Demonstrated to users independently
-->

### User Story 1 - [Brief Title] (Priority: P1)

[Describe this user journey in plain language]

**Why this priority**: [Explain the value and why it has this priority level]

**Independent Test**: [Describe how this can be tested independently - e.g.,
"Can be fully tested by [specific action] and delivers [specific value]"]

**Acceptance Scenarios**:

1. **Given** [initial state], **When** [action], **Then** [expected outcome]
2. **Given** [initial state], **When** [action], **Then** [expected outcome]

---

### User Story 2 - [Brief Title] (Priority: P2)

[Describe this user journey in plain language]

**Why this priority**: [Explain the value and why it has this priority level]

**Independent Test**: [Describe how this can be tested independently]

**Acceptance Scenarios**:

1. **Given** [initial state], **When** [action], **Then** [expected outcome]

---

### User Story 3 - [Brief Title] (Priority: P3)

[Describe this user journey in plain language]

**Why this priority**: [Explain the value and why it has this priority level]

**Independent Test**: [Describe how this can be tested independently]

**Acceptance Scenarios**:

1. **Given** [initial state], **When** [action], **Then** [expected outcome]

---

[Add more user stories as needed, each with an assigned priority]

### Edge Cases

- What happens when one peer rotates keys, publishes a new root, or goes
  offline after previously issuing proofs?
- How does the feature behave when two peers provide conflicting facts about
  the same subject?
- What happens when a certified derived fact points to stale, revoked, or
  superseded input proofs?
- What happens when the verifier has only the exported proof bundle and no
  access to the full peer logs?
- How are freshness gaps, missing peer acknowledgements, revoked commitments,
  or delayed proof publication disclosed to the user?

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST define the peers that participate in the protocol and
  the facts each peer is authoritative for.
- **FR-002**: System MUST define the protocol action or message flow that
  produces the user-visible claim.
- **FR-003**: System MUST expose a way to produce or retrieve the proof bundle
  needed for that claim.
- **FR-004**: Users MUST be able to determine which peer roots, epochs, or
  checkpoints the bundle depends on and what it does not cover.
- **FR-005**: System MUST preserve enough information for an independent
  verifier to evaluate the shared claim from the exported bundle.
- **FR-006**: If the protocol uses certified derived facts, system MUST expose
  the certification payload and its referenced input commitments.

### Trust & Verification Requirements

- **TVR-001**: The feature MUST define the canonical leaf and root semantics for
  each peer involved in the claim.
- **TVR-002**: The feature MUST define what trust assumptions remain after
  verification and what assumptions are removed.
- **TVR-003**: The feature MUST disclose freshness, key-rotation, publication,
  and retention limits that affect the claim.
- **TVR-004**: The feature MUST specify how updates, revocations,
  supersessions, or conflicting peer statements affect previously issued proof
  bundles.
- **TVR-005**: The feature MUST support independent partial verification
  without requiring access to hidden coordinator, witness, or operator state.
- **TVR-006**: The feature MUST define why a privileged coordinator is not
  needed for semantic truth in the protocol.
- **TVR-007**: If certified derived facts are used, the feature MUST define
  whether downstream verifiers trust the certifier, audit through to the
  inputs, or can do both.

### Key Entities *(include if feature involves data)*

- **Peer Root**: [The commitment published by one peer over its facts]
- **Canonical Fact**: [The normalized fact one peer commits to]
- **Proof Bundle**: [The portable package a user exports or shares]
- **Certification Fact**: [A derived fact that pins verified inputs and a rule]
- **Shared Claim**: [The cross-peer statement derived from multiple proofs]
- **Verification Result**: [The user-facing pass/fail or discrepancy outcome]

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A target user can obtain the primary proof bundle for the
  supported protocol without manual reconstruction of the underlying peer logs.
- **SC-002**: An independent verifier can confirm or reject the primary shared
  claim using the exported artifact and documented trust inputs.
- **SC-003**: Replaying the same peer inputs produces the same roots, proofs,
  and verification outcome for the same claim.
- **SC-004**: The feature makes missing peers, freshness gaps, or degraded
  proof conditions visible enough that a verifier can tell when a claim is
  incomplete.

## Assumptions

- [Assumption about which peers publish roots and with what cadence]
- [Assumption about key distribution or trusted root material]
- [Assumption about proof bundle retention or export expectations]
- [Assumption about whether downstream users trust certifiers directly or audit
  through them]
- [Assumption about what remains out of scope for the initial shared claim]
