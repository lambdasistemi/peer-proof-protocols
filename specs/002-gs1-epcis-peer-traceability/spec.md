# Feature Specification: GS1 EPCIS Peer Traceability Proofs

**Feature Branch**: `002-gs1-epcis-peer-traceability`
**Created**: 2026-04-24
**Status**: Draft
**Input**: User description: "Move on with PPP specs, starting from a
peer-only protocol where multiple actors author their own Merkle state and
exchange facts with proofs."

## System Context *(mandatory)*

**Protocol / Domain**: GS1 EPCIS-style supply-chain traceability across
organizations that already exchange visibility events for products, assets, or
documents.

**Participants**: Product owners, manufacturers, logistics providers,
warehouses, receivers, retailers, auditors, insurers, and optional certifiers.
Each operational participant is authoritative only for the events, statuses,
and exceptions it observes or issues. Receivers, auditors, downstream
customers, and certifiers verify proof bundles from the relevant peers.

**Shared Claim**: For a shipment, batch, asset, or trade item, the disclosed
traceability segment is supported by peer-authored event facts from the named
participants, is fresh enough for the stated policy, and has no disclosed or
provable blocking condition within the declared scope.

**Peer-Owned Facts**: Each peer owns its own canonical traceability facts:
commissioning, packing, shipping, receiving, custody handoff, inspection,
transformation, aggregation, deaggregation, disposition, hold, release,
exception, recall, rejection, destruction, or other status facts that the peer
is qualified to assert. No peer can commit facts on behalf of another peer.

**Derived / Certified Claim**: A peer or certifier may publish a certification
fact such as "traceability segment complete under policy P", "custody transfer
accepted", "cold-chain segment passed", "shipment eligible for settlement", or
"no blocking exception found in the disclosed scope". The certification fact
must pin the input proof bundle digest, input peer roots, policy identifier,
policy version, certifier identity, result, and certification epoch.

**Trust Problem**: EPCIS-style data sharing can show useful visibility events,
but a verifier often receives excerpts without a durable way to know which
peer committed them, which root or epoch they came from, whether relevant
negative facts were excluded, and whether a derived claim was based on stable
inputs. A central aggregator can solve this by becoming the trusted oracle, but
PPP should preserve peer authorship instead.

**Primary Artifact**: Traceability proof bundle containing disclosed facts,
inclusion proofs, exclusion or completeness evidence, peer root references,
peer signatures or key references, schema and vocabulary versions, policy
metadata, and optional certification facts.

**Verification Audience**: Receivers accepting goods, downstream customers
checking provenance, auditors reviewing compliance evidence, insurers
evaluating claims, customs or trade counterparties checking document trails,
and certifiers promoting verified bundles into reusable facts.

**Non-Goals**: Replacing EPCIS, becoming a GS1 conformance authority,
republishing GS1 standards text, acting as an industry registry, proving the
physical truth of real-world events, detecting counterfeit goods by itself, or
requiring every participant to disclose its full event log.

## Data Flow *(mandatory for PPP)*

1. A peer observes or issues a traceability event inside its own domain and
   maps that event to a canonical traceability fact.
2. The peer commits the fact into its own authenticated state and publishes or
   provides a signed root checkpoint for the relevant epoch and scope.
3. A claimer assembles a traceability proof bundle from its own facts and any
   proof bundles received from counterparties, such as suppliers, carriers, or
   receivers.
4. The claimer sends the bundle to a verifier. The bundle carries the disclosed
   facts, proof paths, peer root references, interpretation metadata, and any
   exclusion or completeness evidence needed by the declared policy.
5. The verifier checks peer identities or trust anchors, root signatures,
   inclusion proofs, exclusion proofs where applicable, freshness, policy
   versions, and cross-peer consistency.
6. If a certifier verifies the bundle and applies a rule, the certifier may
   publish a certification fact into its own tree. Downstream verifiers can
   either trust that certifier root or audit through to the pinned input proofs.
7. If facts are updated, revoked, superseded, or contradicted, peers publish new
   roots and future bundles must disclose whether older proofs are still valid,
   stale, superseded, or incomplete under the relevant policy.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Verify an Inbound Traceability Segment (Priority: P1)

As a receiver, I want an inbound shipment proof bundle from my supplier and
carrier, so I can verify the disclosed handoff and status facts before
accepting the goods or escalating a discrepancy.

**Why this priority**: This is the minimum useful peer-proof flow. It involves
at least two independent peers, a real operational decision, and a verifier who
should not trust a single platform view.

**Independent Test**: A receiver can inspect a proof bundle for one shipment
segment, verify each included fact against the named peer roots, and reach a
clear result: accepted, rejected, incomplete, stale, or conflicted.

**Acceptance Scenarios**:

1. **Given** a shipment with supplier shipping facts and carrier custody facts,
   **When** the receiver verifies the inbound bundle, **Then** the receiver can
   identify which peer committed each fact, which roots support the facts, and
   whether the stated handoff claim passes.
2. **Given** a shipment bundle missing the carrier root or containing a stale
   supplier root, **When** the receiver verifies the bundle, **Then** the result
   is incomplete or stale rather than accepted.
3. **Given** supplier and carrier facts that disagree about the handoff subject
   or declared event scope, **When** the receiver verifies the bundle, **Then**
   the discrepancy is surfaced as a conflict with the responsible peer facts
   identified.

---

### User Story 2 - Selectively Disclose Provenance to a Customer (Priority: P2)

As a supplier, I want to share only the traceability facts required by a
customer policy, so I can prove provenance without exposing my full event
history, unrelated trading partners, or internal operational metadata.

**Why this priority**: Selective disclosure is a central reason to use Merkle
state rather than forwarding full event logs or relying only on detached
signatures.

**Independent Test**: A supplier can export a proof bundle for a defined
subject and policy, omit unrelated facts, and still let the customer verify the
included facts and disclosed exclusions against signed roots.

**Acceptance Scenarios**:

1. **Given** a customer requests provenance for one batch, **When** the supplier
   exports a proof bundle, **Then** the bundle includes only the required facts,
   the roots they depend on, and the declared coverage limits.
2. **Given** the customer requires proof that the supplier has no active hold
   or recall fact for the batch in the disclosed scope, **When** the supplier
   exports the bundle, **Then** the bundle includes exclusion or completeness
   evidence for that negative claim or reports that the claim cannot be proven.

---

### User Story 3 - Compose Proofs Into a Certification Fact (Priority: P3)

As a certifier or qualified peer, I want to verify supplier, carrier, and
receiver proof bundles and publish my own certification fact, so downstream
verifiers can reuse that result with an explicit choice to trust me or audit
through to the input proofs.

**Why this priority**: Composition is the main PPP pattern that turns verified
bundles into reusable higher-order facts without creating a single global
truth owner.

**Independent Test**: A certifier can verify a multi-peer input bundle, apply a
named policy, publish a certification fact in its own tree, and let a downstream
verifier either verify only the certification fact or audit through to the
input peer proofs.

**Acceptance Scenarios**:

1. **Given** valid supplier, carrier, and receiver bundles for a declared
   shipment segment, **When** the certifier applies policy P, **Then** the
   certifier can publish a certification fact that pins the policy, result,
   input bundle digest, input peer roots, and certification epoch.
2. **Given** a certification fact whose referenced input root has been
   superseded or revoked, **When** a downstream verifier audits through the
   certification, **Then** the verifier sees the certification as stale,
   superseded, or discrepant according to the documented policy.

---

### User Story 4 - Prove Absence of a Blocking Condition (Priority: P4)

As a verifier, I want to check that no active hold, recall, dispute, rejection,
or exception fact exists for a subject within a declared peer scope, so I can
distinguish a clean claim from a merely omitted bad fact.

**Why this priority**: Exclusion is where signed state becomes materially more
useful than signed positive messages. A verifier needs to know whether absence
is proven, scoped, stale, or unsupported.

**Independent Test**: A verifier can evaluate a bundle containing a negative
claim and determine whether the claim is proven for the declared root and key
range, impossible to prove with the provided data, or contradicted by an
included blocking fact.

**Acceptance Scenarios**:

1. **Given** a supplier claims there is no active recall for a batch under its
   current root, **When** the verifier checks the bundle, **Then** the verifier
   can validate the exclusion proof or reject the negative claim as unsupported.
2. **Given** the supplier discloses a blocking exception fact for the same
   subject and scope, **When** the verifier checks the bundle, **Then** the
   negative claim fails with the conflicting fact identified.

### Edge Cases

- One peer rotates signing keys after issuing roots used by an older bundle.
- One peer goes offline after publishing a root needed by a downstream
  verifier.
- Two peers provide facts for the same transition with mismatched subjects,
  timestamps, locations, or declared scopes.
- A peer can prove inclusion for positive facts but cannot prove exclusion for
  negative claims because its tree or publication policy does not support that
  query.
- A certification fact references input proofs that are later superseded,
  revoked, or found incomplete.
- A verifier receives only the exported proof bundle and cannot query the
  underlying EPCIS systems.
- A peer withholds a relevant fact; verification can detect only what is
  contradicted by commitments, exclusions, completeness statements, or policy
  declarations.
- A root is valid but too old for the receiver's policy.
- A disclosed fact uses a vocabulary or policy version the verifier does not
  recognize.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST define every peer role participating in a
  traceability claim and the fact categories each role is authoritative for.
- **FR-002**: System MUST define canonical traceability fact types for the
  supported EPCIS-style event subset without requiring peers to disclose their
  full event logs.
- **FR-003**: Each peer MUST publish or provide signed root checkpoints for its
  own traceability facts and MUST NOT commit facts on behalf of another peer.
- **FR-004**: System MUST define a traceability proof bundle that carries
  disclosed facts, proof paths, peer root references, peer identity references,
  schema or vocabulary versions, policy metadata, and coverage limits.
- **FR-005**: System MUST support verification of positive inclusion facts and
  MUST explicitly support, reject, or scope negative claims such as "not held",
  "not recalled", "not disputed", or "not rejected".
- **FR-006**: System MUST define how a shared traceability claim is evaluated
  from at least two peer-owned roots.
- **FR-007**: System MUST define certification facts for derived claims and the
  required payload fields that pin the verified inputs.
- **FR-008**: System MUST classify verification outcomes as pass, fail,
  incomplete, stale, conflicted, unsupported, or superseded.
- **FR-009**: System MUST let a proof bundle disclose only the facts required
  for the declared subject, scope, and policy.
- **FR-010**: System MUST preserve enough information for an independent
  verifier to evaluate the bundle without hidden coordinator state or access to
  the source peer systems.
- **FR-011**: System MUST identify the source vocabulary, schema, policy, and
  version used to interpret each disclosed fact and certification.
- **FR-012**: System MUST make missing peers, missing roots, invalid proofs,
  stale roots, and unsupported exclusions visible as explicit verification
  results rather than generic failure.

### Trust & Verification Requirements

- **TVR-001**: The feature MUST define canonical leaf semantics for each
  supported traceability fact type, including subject identity, event identity,
  peer identity, declared scope, fact time, commitment time, status, and version
  fields.
- **TVR-002**: The feature MUST define root semantics for each peer, including
  whether a root commits to active state, append-only event history, scoped
  subject indexes, exclusion indexes, or a documented combination of these.
- **TVR-003**: The feature MUST define how peer roots are signed, how verifier
  trust anchors are identified, and how key rotation affects older bundles.
- **TVR-004**: The feature MUST disclose freshness, publication cadence,
  retention, and proof-regeneration limits for every peer root used by a claim.
- **TVR-005**: The feature MUST specify how updates, revocations,
  supersessions, blocking exceptions, and conflicting peer statements affect
  previously issued proof bundles and certification facts.
- **TVR-006**: The feature MUST support independent partial verification
  without requiring a central registry, privileged aggregator, witness, or
  operator to decide semantic truth.
- **TVR-007**: The feature MUST state which trust assumptions remain after
  verification, including that a valid commitment does not prove the physical
  event happened, only that the named peer committed the canonical fact under
  the stated root.
- **TVR-008**: If certification facts are used, the feature MUST define whether
  downstream verifiers trust the certifier, audit through to the input proofs,
  or can choose either mode with different trust disclosures.
- **TVR-009**: For exclusion claims, the feature MUST define the committed key
  space, subject scope, and completeness assumptions that make absence
  meaningful.
- **TVR-010**: The feature MUST define why detached signatures alone are
  insufficient for the supported claim, especially for selective disclosure,
  freshness, exclusion, and reusable certification.

### Key Entities *(include if feature involves data)*

- **Traceability Peer**: An organization or actor that owns a subset of
  traceability facts and signs roots over its own state.
- **Peer Root Checkpoint**: A signed commitment by one traceability peer over a
  declared fact set, subject index, event history, exclusion index, or policy
  scope.
- **Canonical Traceability Fact**: A normalized peer-authored fact derived from
  an EPCIS-style event, status, or exception for a declared subject and scope.
- **Traceability Proof Bundle**: A portable package containing disclosed facts,
  inclusion proofs, exclusion or completeness evidence, root references,
  interpretation metadata, and verification policy references.
- **Blocking Condition**: A peer-authored fact such as hold, recall, dispute,
  rejection, exception, or revocation that weakens or defeats the shared claim
  inside a declared scope.
- **Certification Fact**: A derived peer-authored fact that states the result
  of applying a named rule to verified input proof bundles.
- **Shared Traceability Claim**: A cross-peer statement derived from two or more
  peer roots, such as handoff accepted, segment complete, no blocking exception
  found, or eligible for settlement.
- **Verification Result**: The user-facing outcome explaining whether the claim
  passed, failed, is incomplete, stale, conflicted, unsupported, or superseded.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A receiver can obtain a traceability proof bundle for one
  shipment segment involving at least two peers without manually reconstructing
  the underlying peer event logs.
- **SC-002**: An independent verifier can confirm or reject the primary shared
  traceability claim using only the exported artifact, trusted peer keys or
  trust anchors, and the documented interpretation rules.
- **SC-003**: Replaying the same disclosed peer facts, roots, and policy inputs
  produces the same proof verification outcome for the same claim.
- **SC-004**: The verifier can distinguish at least five non-pass outcomes:
  missing peer, missing root, stale root, conflicting fact, and unsupported
  exclusion.
- **SC-005**: A certifier can publish a certification fact for a verified
  multi-peer bundle, and a downstream verifier can choose between `trust
  certifier` and `audit through` verification modes.
- **SC-006**: A negative claim such as "no active hold for subject S under
  peer P at root R" either verifies against the declared committed scope or is
  explicitly rejected as unsupported or incomplete.
- **SC-007**: The specification makes GS1-related boundaries clear enough that
  implementers can build an interoperable layer without republishing standards
  text or presenting the project as a GS1 conformance authority.

## Assumptions

- Participating organizations already produce or consume EPCIS-style visibility
  data and can map their relevant events into canonical peer facts.
- Peers can sign root checkpoints and distribute trust anchors to counterparties
  through existing business, legal, credential, or governance channels.
- The initial protocol targets bounded traceability segments rather than a
  complete end-to-end global supply-chain record.
- The meaning of "complete", "fresh", "accepted", "blocked", or "eligible"
  depends on an explicit policy identifier and version.
- A witness, timestamping, or publication layer may improve availability, but
  it is not authoritative for semantic truth.
- Verification cannot prove that a real-world event physically occurred; it
  proves that the named peer committed the disclosed fact under the stated root
  and that the declared cross-peer rule evaluates as documented.
