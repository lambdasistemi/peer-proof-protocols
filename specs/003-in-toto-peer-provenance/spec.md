# Feature Specification: in-toto Peer Provenance Proofs

**Feature Branch**: `003-in-toto-peer-provenance`
**Created**: 2026-04-24
**Status**: Draft
**Input**: User description: "Spec and plan the other app before creating a PR
so both can be reviewed together."

## System Context *(mandatory)*

**Protocol / Domain**: in-toto software supply-chain provenance, including
layouts, link metadata, attestations, statements, predicates, envelopes, and
attestation bundles used around source, build, test, scan, package, and release
workflows.

**Participants**: Project owner, source maintainer, builder, tester, scanner,
packager, publisher, release manager, downstream verifier, and optional
certifier. Each functionary or service is authoritative only for the steps,
artifacts, scans, approvals, revocations, or policy results it performs or
issues.

**Shared Claim**: A software artifact identified by digest was produced,
checked, packaged, or released through the declared supply-chain steps under
policy P, with required peer-authored evidence available, fresh enough, and no
proven blocking condition in the declared scope.

**Peer-Owned Facts**: Project owners own layout and policy facts. Source peers
own source and review facts. Builders own build provenance facts. Testers and
scanners own test, SBOM, vulnerability, and runtime-trace facts. Publishers own
release and distribution facts. Each peer owns revocation, exception, key
rotation, or supersession facts for its own assertions.

**Derived / Certified Claim**: A verifier, release manager, or certifier may
publish a certification fact such as "artifact digest D satisfies release
policy P", "SLSA provenance accepted", "no blocking vulnerability claim is
active in scope S", or "release candidate eligible for promotion". The
certification fact must pin the input bundle digest, input peer roots,
predicate and policy identifiers, policy version, certifier identity, result,
and certification epoch.

**Trust Problem**: in-toto already signs statements and links, but a verifier
can still receive incomplete, stale, replayed, or selectively edited sets of
attestations. Detached signatures prove who signed an attestation; they do not
by themselves prove the current peer state, absence of blocking facts, bundle
freshness, or completeness of the declared evidence scope. PPP should add
peer-owned authenticated state and portable proof bundles without introducing a
central provenance oracle.

**Primary Artifact**: Provenance proof bundle containing selected in-toto
statements or references, canonical peer facts, inclusion proofs, scoped
exclusion evidence, peer root checkpoints, trust-anchor references, policy
metadata, and optional certification facts.

**Verification Audience**: Package consumers, release managers, security
teams, auditors, deployment gates, package managers, customers, and downstream
certifiers who need to verify artifact provenance without reconstructing every
peer database.

**Non-Goals**: Replacing in-toto, defining a competing attestation framework,
becoming a mandatory transparency log, proving source code is correct, proving
vulnerability scanners are complete, requiring full disclosure of private build
logs, or centralizing semantic truth in a coordinator.

## Data Flow *(mandatory for PPP)*

1. A project owner publishes a signed layout or policy and commits its canonical
   policy fact into the owner's peer state.
2. Each functionary performs its step and issues in-toto link metadata,
   attestation statements, or predicate-specific evidence for the artifacts it
   touches.
3. Each functionary maps its evidence into canonical peer facts and commits
   those facts, plus any revocation or exception facts, into its own
   authenticated state.
4. A release manager or claimer assembles a provenance proof bundle for one
   artifact digest from the required peer facts, in-toto evidence, proofs, root
   checkpoints, policy metadata, and declared coverage limits.
5. A verifier checks in-toto signatures and predicate semantics where needed,
   then checks peer roots, inclusion proofs, exclusion proofs, freshness, policy
   version, and cross-peer consistency.
6. If a certifier verifies the bundle and applies a policy, the certifier may
   commit a certification fact into its own tree.
7. Downstream verifiers can either trust the certifier's peer root for the
   certified result or audit through to the pinned input proofs and in-toto
   attestations.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Verify a Release Artifact Across Peers (Priority: P1)

As a package consumer or deployment gate, I want to verify a proof bundle for an
artifact digest, so I can confirm that source, build, test, scan, and release
evidence came from the expected peers and satisfies the declared policy.

**Why this priority**: This is the core software supply-chain value. It uses
in-toto's existing step evidence while adding peer-owned roots and bundle
freshness around the selected proof set.

**Independent Test**: A verifier can load a bundle for one artifact, validate
each required peer root and proof, and return `pass`, `fail`, `incomplete`,
`stale`, `conflicted`, `unsupported`, or `superseded`.

**Acceptance Scenarios**:

1. **Given** a bundle with project-owner, builder, tester, scanner, and
   publisher roots, **When** the verifier checks the artifact digest, **Then**
   each required fact is linked to the peer that authored it and the shared
   release claim passes only if the policy evaluates successfully.
2. **Given** a bundle missing the scanner root required by policy P, **When**
   the verifier checks the artifact digest, **Then** the result is incomplete
   rather than pass.
3. **Given** a build attestation for a different artifact digest, **When** the
   verifier checks the bundle, **Then** the artifact subject mismatch is
   reported as a failure.

---

### User Story 2 - Prove Absence of Blocking Supply-Chain Conditions (Priority: P2)

As a security reviewer, I want to verify that no active revocation, failed test,
critical vulnerability, compromised-key notice, or release hold exists for an
artifact in the declared peer scope, so omitted bad evidence is not treated as
good evidence.

**Why this priority**: Exclusion is the main reason signed state improves on
ordinary signed attestations. The verifier must know when absence is proven,
unsupported, stale, or contradicted.

**Independent Test**: A verifier can evaluate negative claims for one artifact
and distinguish valid exclusion proofs from unsupported omissions or active
blocking facts.

**Acceptance Scenarios**:

1. **Given** a scanner claims no active critical vulnerability result for
   artifact D under root R, **When** the verifier checks the exclusion proof,
   **Then** the negative claim passes only if the root semantics support that
   committed key space.
2. **Given** the same bundle includes a signed failed-test fact for artifact D,
   **When** the verifier evaluates policy P, **Then** the negative claim fails
   or conflicts with the blocking fact identified.

---

### User Story 3 - Selectively Disclose Provenance Evidence (Priority: P3)

As a release manager, I want to disclose only the provenance evidence required
by a customer or deployment policy, so I can prove the artifact's release claim
without exposing unrelated internal build logs, unreleased artifact names, or
private scan detail.

**Why this priority**: Selective disclosure is where peer-owned Merkle state
adds value without replacing in-toto's existing statement and predicate model.

**Independent Test**: A release manager can export a bundle for one artifact and
policy, omit unrelated attestations, and still let the verifier validate the
included facts and declared exclusions.

**Acceptance Scenarios**:

1. **Given** a customer requires source, build, test, and release facts for
   artifact D, **When** the release manager exports a bundle, **Then** unrelated
   artifacts and private step metadata are not required for verification.
2. **Given** a required fact is intentionally redacted, **When** the verifier
   checks the bundle, **Then** the redaction is surfaced as incomplete or
   unsupported according to policy.

---

### User Story 4 - Certify a Provenance Policy Result (Priority: P4)

As a certifier or release authority, I want to verify an input proof bundle and
publish a certification fact, so downstream verifiers can reuse the policy
result while choosing whether to trust me or audit through to the input proofs.

**Why this priority**: Certification facts are the PPP composition model. They
turn verified multi-peer evidence into reusable facts without making the
certifier the owner of the underlying peer facts.

**Independent Test**: A certifier can verify one input bundle, publish a
certification fact in its own tree, and a downstream verifier can validate the
certification directly or audit through to the pinned inputs.

**Acceptance Scenarios**:

1. **Given** a valid provenance proof bundle for artifact D, **When** a
   certifier applies policy P, **Then** the certification fact pins the input
   bundle digest, input peer roots, policy identifier, result, and epoch.
2. **Given** a certification fact references an input root later superseded by a
   revocation fact, **When** a downstream verifier audits through, **Then** the
   certification is reported as stale, superseded, or conflicted according to
   policy.

### Edge Cases

- A peer rotates keys after issuing attestations or root checkpoints.
- A layout or policy expires before the verifier receives the bundle.
- A bundle contains valid in-toto signatures but omits a required peer root.
- A bundle contains a valid attestation that is obsolete under the peer's newer
  root.
- Two builders claim different outputs for the same step and artifact subject.
- A verifier does not recognize one predicate type in the bundle.
- A peer can prove inclusion but cannot prove exclusion for a required negative
  claim.
- A certifier is trusted by one downstream verifier but not another.
- A bundle is valid for one artifact digest but replayed against another.
- A witness or storage system is unavailable, but the exported bundle is still
  available.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST define the software supply-chain peer roles and the
  provenance facts each role is authoritative for.
- **FR-002**: System MUST map supported in-toto layouts, links, statements,
  predicates, envelopes, or bundles into canonical peer facts without requiring
  the project to replace in-toto.
- **FR-003**: Each peer MUST publish or provide signed root checkpoints for its
  own provenance, revocation, exception, and certification facts.
- **FR-004**: System MUST define a provenance proof bundle that carries
  selected in-toto evidence, canonical peer facts, proof paths, peer root
  references, trust-anchor references, policy metadata, and coverage limits.
- **FR-005**: System MUST support inclusion verification for required positive
  facts and explicit `unsupported` or `incomplete` outcomes for negative claims
  that lack valid exclusion evidence.
- **FR-006**: System MUST define how a shared artifact provenance claim is
  evaluated from at least two peer-owned roots.
- **FR-007**: System MUST define certification facts for derived provenance
  policy results and the payload fields that pin verified inputs.
- **FR-008**: System MUST classify verification outcomes as pass, fail,
  incomplete, stale, conflicted, unsupported, or superseded.
- **FR-009**: System MUST support selective disclosure for one artifact subject
  and declared policy without requiring disclosure of unrelated attestations.
- **FR-010**: System MUST preserve enough information for an independent
  verifier to evaluate the bundle without hidden coordinator state.
- **FR-011**: System MUST identify in-toto statement, predicate, envelope,
  layout, policy, and PPP schema versions used to interpret the evidence.
- **FR-012**: System MUST make missing peers, missing roots, stale roots,
  unsupported predicates, unsupported exclusions, and conflicting peer facts
  explicit in verification results.

### Trust & Verification Requirements

- **TVR-001**: The feature MUST define canonical leaf semantics for supported
  provenance facts, including artifact subject, digest, step, peer identity,
  predicate type, policy scope, fact time, commitment time, and version fields.
- **TVR-002**: The feature MUST define peer root semantics, including whether a
  root commits to step history, active artifact state, revocation state,
  exclusion indexes, certification facts, or a documented combination.
- **TVR-003**: The feature MUST define how peer roots are signed, how verifier
  trust anchors are identified, and how key rotation affects older bundles.
- **TVR-004**: The feature MUST disclose freshness, publication cadence,
  retention, proof-regeneration, and policy-expiration limits.
- **TVR-005**: The feature MUST specify how revocations, failed checks,
  supersessions, policy updates, predicate deprecations, and conflicting peer
  statements affect previously issued bundles and certifications.
- **TVR-006**: The feature MUST support independent partial verification
  without requiring a central transparency log, registry, package repository, or
  coordinator to decide semantic truth.
- **TVR-007**: The feature MUST state that valid verification proves peer
  commitments and policy evaluation over disclosed evidence, not that software
  is safe, correct, vulnerability-free, or malicious-code-free.
- **TVR-008**: If certification facts are used, the feature MUST define whether
  downstream verifiers trust the certifier, audit through to the input proofs,
  or can choose either mode with different trust disclosures.
- **TVR-009**: For exclusion claims, the feature MUST define the committed key
  space, artifact scope, peer scope, and completeness assumptions that make
  absence meaningful.
- **TVR-010**: The feature MUST define why signed attestations alone are
  insufficient for the supported claim, especially for freshness, exclusion,
  bundle completeness, selective disclosure, and reusable certification.

### Key Entities *(include if feature involves data)*

- **Provenance Peer**: A project owner, functionary, service, publisher, or
  certifier that owns and signs roots over its own provenance facts.
- **Peer Root Checkpoint**: A signed commitment by one peer over a declared
  provenance fact set, active artifact state, revocation index, exclusion
  index, or certification scope.
- **Artifact Subject**: The software artifact identified by digest that the
  shared claim is about.
- **Canonical Provenance Fact**: A normalized peer-authored fact derived from
  in-toto metadata, predicate evidence, revocation records, policy state, or
  certification output.
- **Attestation Reference**: A pointer or embedded excerpt linking a canonical
  fact to its in-toto statement, predicate, envelope, link, layout, or bundle
  evidence.
- **Blocking Condition**: A failed test, active vulnerability threshold breach,
  revoked attestation, compromised key notice, release hold, policy exception,
  or other peer-authored condition that weakens or defeats the claim.
- **Provenance Proof Bundle**: A portable package containing selected evidence,
  peer roots, inclusion proofs, exclusion evidence, policy metadata, and
  verification coverage.
- **Certification Fact**: A derived peer-authored fact that states the result of
  applying a named provenance policy to verified input proof bundles.
- **Verification Result**: The user-facing outcome explaining whether the
  provenance claim passed, failed, is incomplete, stale, conflicted,
  unsupported, or superseded.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A verifier can obtain a provenance proof bundle for one artifact
  involving at least two peer roots without manually reconstructing every
  functionary database or attestation store.
- **SC-002**: An independent verifier can confirm or reject the primary shared
  provenance claim using only the exported bundle, trusted peer keys or trust
  anchors, and documented interpretation rules.
- **SC-003**: Replaying the same disclosed peer facts, roots, in-toto evidence,
  and policy inputs produces the same verification outcome.
- **SC-004**: The verifier can distinguish at least six non-pass outcomes:
  missing peer, missing root, stale root, unsupported predicate, conflicting
  fact, and unsupported exclusion.
- **SC-005**: A negative claim such as "no active revocation for artifact D
  under scanner peer P at root R" either verifies against the committed scope or
  is rejected as unsupported or incomplete.
- **SC-006**: A certifier can publish a certification fact for a verified
  multi-peer provenance bundle, and a downstream verifier can choose between
  `trust certifier` and `audit through`.
- **SC-007**: The specification makes in-toto boundaries clear enough that PPP
  implementers extend or wrap in-toto evidence without claiming to replace
  in-toto or redefine its predicate ecosystem.

## Assumptions

- Participating projects already use or can export in-toto-compatible metadata
  for at least some supply-chain steps.
- Peers can sign root checkpoints and distribute trust anchors through existing
  project, organization, package, or customer channels.
- The first protocol targets one artifact digest and bounded policy, not a full
  ecosystem-wide package transparency system.
- The meaning of "complete", "fresh", "eligible", "blocked", or "accepted"
  depends on an explicit policy identifier and version.
- Witnesses, package registries, or transparency logs may improve availability,
  but they are not authoritative for semantic truth in this protocol.
- Verification cannot prove the artifact is safe or correct; it proves that the
  named peers committed the disclosed facts under stated roots and that the
  declared policy evaluates as documented.
