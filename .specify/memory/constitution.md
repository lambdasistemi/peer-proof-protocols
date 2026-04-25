<!--
Sync Impact Report
Version change: 3.1.0 -> 3.1.1
Modified principles:
- None
Modified sections:
- Title and repository naming
- Product Scope & Non-Goals clarification
Templates requiring updates:
- None
Follow-up TODOs:
- None
-->
# Peer Proof Protocols Constitution

## Core Principles

### I. Peers Before Platforms
Every feature MUST target a peer protocol in which multiple peers exchange
claims, acknowledgements, or proofs from their own authenticated state. Product
surfaces remain in scope, but they are secondary to the peer protocol
definition. A proposal centered on a centralized oracle, adapter, workflow
overlay, or coordinator-owned state is out of scope for this repository unless
it appears only as comparative input or migration context.

### II. One Peer, One Tree
Each peer MUST be authoritative only for its own facts and MUST author its own
Merkle-authenticated state. No peer may rewrite another peer's commitments, and
no shared root may replace the need for peer-owned roots. Shared outcomes MUST
be derived from independently authored trees, not from a hidden global truth
service. Features that do not need peer-owned state continuity SHOULD prefer
plain signatures and are not good fits for this repository.

### III. Deterministic Peer Commitments
Every peer-authored fact set MUST reduce to deterministic leaves, roots, and
proof bundle bytes. Given the same peer inputs, independent implementations
MUST derive the same commitment material, root transitions, and verification
results. Specs and plans MUST define ordering, normalization, omission
handling, versioning, and replay behavior well enough to support golden
fixtures and reproducible verification.

### IV. Peer-Carried Proof Bundles and Certifications
Protocol messages MUST carry, or deterministically reference, the proofs needed
for counterparties and external verifiers to validate the claim in question.
The verifier SHOULD check only the received slices against trusted peer roots
or published peer checkpoints; rebuilding the full network state is optional,
not a baseline requirement. Browser, CLI, API, and contract verifiers are
first-class outputs, not secondary tooling. When a peer derives a higher-order
claim from verified inputs, that peer MAY promote the result into a new
certified fact in its own tree, but only if the certification pins the verified
inputs and rule used to derive it.

### V. No Privileged Coordinator
Every feature MUST make coordination optional, narrow, and non-authoritative.
Witnesses, relays, settlement layers, or publication channels may order, relay,
timestamp, or escrow, but they MUST NOT decide semantic truth for peer-owned
facts and MUST NOT own the only Merkle tree that matters. Hidden coordinator
trust is a protocol bug, not a documentation detail.

## Product Scope & Non-Goals

The primary repository outputs are a research proposal, protocol
specifications, peer models, canonical schemas, proof bundle formats, verifier
rules, and reference flows for MPF/MPFS-native peer applications in which each
peer authors its own tree.

Non-goals for the first product generations:

- centralized SaaS overlay products, single-oracle adapters, or coordinator
  services as primary outputs
- protocols where detached signatures are already sufficient and authenticated
  state continuity adds no material value
- allowing a coordinator, witness, marketplace, or registry to own or rewrite
  peer state
- designing polished application UX before the protocol, peer model, and
  proof flow are legible

Each feature SHOULD deliver one high-value, legible shared claim first, such as
milestone payable, custody transferred, build provenance complete, or
credential valid, before broadening scope.

## Composition & Certification

Composition in this repository is semantic and attestational, not assumed proof
compression. A feature MAY define a certification ladder in which:

- base facts are disclosed with proofs
- a peer verifies those proofs and applies an explicit predicate or policy
- the peer publishes the derived result as a new certified fact in its own tree

When a feature introduces certified derived facts, it MUST define the minimum
certification payload:

- subject identifier
- rule or policy identifier and version
- result or classification
- input bundle digest or canonical input set digest
- input roots, checkpoints, or equivalent trusted commitment references
- certifier identity
- certification time or epoch

The feature MUST also define the downstream verification mode:

- `trust certifier`: downstream users verify the certification fact and the
  certifier's root only
- `audit through`: downstream users can traverse from the certification to the
  referenced input bundle and re-check the underlying proofs
- `mixed`: both paths are supported, with the weaker path explicitly disclosed

Certified facts are useful only when they remain traceable to verified inputs.
An unpinned “all good” statement is not a valid composition artifact.

## Delivery Workflow & Quality Gates

All work MUST start with Spec Kit artifacts. The specification defines the user
story, peers, and protocol problem; the plan defines peer-owned state,
commitment derivation, proof flow, and verification path; implementation
follows only after those artifacts pass review.

Before implementation begins, every plan MUST pass these gates:

- it names at least two peers with disjoint fact authority
- it defines what each peer owns, signs, publishes, and proves
- it defines each peer's root lifecycle and proof publication model
- it explains why Merkle-authenticated state adds value beyond plain message
  signatures
- it specifies the proof bundle exchanged, the shared claim being evaluated,
  and the verifier's partial-verification path
- if derived facts are introduced, it defines the certification payload, rule
  identifier, and downstream audit model
- it documents any witness, relay, or settlement assumptions as narrow,
  non-authoritative dependencies
- it documents freshness limits, dispute handling, and degraded modes
- it includes golden fixture coverage and negative verification cases for
  multi-peer proof bundles and root transitions

A feature is not done until repository docs show how an external verifier can
reproduce at least one representative cross-peer claim independently from
exported proofs and documented trust inputs.

## Governance

This constitution overrides local habits, convenience shortcuts, and implicit
team norms for this repository.

Amendment rules:

- amendments MUST be made through a documented change to this file
- any amendment that changes a principle or adds a new governing section MUST
  update affected templates in `.specify/templates/`
- every amendment MUST include a semantic version bump rationale

Versioning policy:

- MAJOR: removes or redefines a principle in a backward-incompatible way
- MINOR: adds a principle, a mandatory gate, or a new governing section
- PATCH: clarifies wording without changing meaning

Compliance expectations:

- every feature plan MUST include an explicit constitution check
- every task list that changes trust claims, canonicalization, or proof bundle
  formats MUST include deterministic fixture and verification work
- exceptions MUST be written down, time-boxed, and approved before work starts

**Version**: 3.1.1 | **Ratified**: 2026-04-23 | **Last Amended**: 2026-04-25
