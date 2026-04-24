# Research: GS1 EPCIS Peer Traceability Proofs

## Decision: Treat EPCIS as the interoperability target, not our protocol

- **Decision**: PPP facts map from EPCIS-style visibility events, but the PPP
  protocol does not replace EPCIS capture, query, or conformance rules.
- **Rationale**: GS1 already defines the visibility event language, JSON/JSON-LD
  syntax, validation artifacts, and REST interface surface. PPP adds peer-owned
  authenticated state and portable proof bundles around selected facts.
- **Alternatives considered**:
  - Define a new traceability event standard: rejected because it would
    duplicate EPCIS and weaken interoperability.
  - Copy GS1 materials into this repository: rejected because implementation is
    in scope, but redistributing standards text or implying GS1 endorsement is
    not.

## Decision: Start with bounded traceability segments

- **Decision**: The first claim type is
  `ppp.gs1_epcis.traceability_segment.v1`.
- **Rationale**: A bounded segment can be independently demonstrated with two
  or three peers and has immediate operational value for receiving, settlement,
  audit, and dispute workflows.
- **Alternatives considered**:
  - Full end-to-end supply-chain provenance: rejected for MVP because it
    creates too many peer and policy dependencies before the proof model is
    validated.
  - Single-peer inventory proof: rejected because signatures or a single peer
    commitment are often enough; PPP is strongest when the claim is cross-peer.

## Decision: Each peer owns its own root

- **Decision**: Supplier, carrier, receiver, and certifier roots remain
  independent. A proof bundle references multiple peer roots instead of
  aggregating them into a registry root.
- **Rationale**: The constitution requires peer-only semantics. The verifier
  should know which organization authored each fact and should not depend on a
  privileged aggregator to decide truth.
- **Alternatives considered**:
  - Shared industry Merkle tree: rejected because it reintroduces a registry or
    oracle as the semantic authority.
  - Claimer-built tree over all received facts: rejected because it hides peer
    authorship and lets the claimer repackage facts without preserving root
    provenance.

## Decision: Separate event-history roots from active-state and exclusion indexes

- **Decision**: A peer root may commit to event history, active subject state,
  scoped exclusion indexes, or a documented combination. The root semantics must
  be declared in each checkpoint.
- **Rationale**: Inclusion of a historical shipping event is different from
  proving no active recall, hold, or dispute exists. Exclusion requires a
  committed key space and cannot be inferred from omitted facts.
- **Alternatives considered**:
  - Append-only history only: rejected because negative claims become weak or
    expensive without additional committed indexes.
  - Active state only: rejected because audit and dispute workflows need
    historical event facts and supersession history.

## Decision: Unsupported exclusions are first-class verifier outcomes

- **Decision**: Negative claims verify only when the bundle includes a valid
  proof for the declared committed key space. Otherwise the verifier returns
  `unsupported` or `incomplete`.
- **Rationale**: This avoids the common false inference that absence from a
  disclosed subset means absence from peer state.
- **Alternatives considered**:
  - Treat missing blocking facts as clean: rejected because it rewards
    selective omission.
  - Require all peers to support every exclusion query in v1: rejected because
    adoption should allow peers to start with inclusion proofs while disclosing
    unsupported negative claims honestly.

## Decision: Proof bundles are portable and verifier-complete

- **Decision**: A traceability proof bundle must carry disclosed facts, proof
  paths, root checkpoints, policy references, vocabulary versions, and coverage
  limits. The verifier should not need the source EPCIS systems.
- **Rationale**: The core user experience is peer-carried facts and proofs. A
  verifier may need trusted peer keys, but not hidden coordinator or operator
  state.
- **Alternatives considered**:
  - Online verification against every peer system: rejected because it makes
    offline audit, customer handoff, and delayed dispute resolution brittle.
  - Screenshots or exported event logs: rejected because they do not preserve
    root provenance, exclusion semantics, or selective disclosure.

## Decision: Composition is a certification fact with pinned inputs

- **Decision**: A certifier or qualified peer may publish
  `ppp.gs1_epcis.certification.v1` after verifying input bundles and applying a
  named policy. The certification fact must pin input bundle digest, input peer
  roots, policy identifier, version, result, certifier identity, and epoch.
- **Rationale**: This creates reusable higher-order facts while preserving an
  audit-through path to base facts.
- **Alternatives considered**:
  - Opaque certificate: rejected because downstream verifiers cannot tell what
    was certified.
  - Mandatory audit-through for every verifier: rejected because some workflows
    legitimately trust a certifier, provided the trust mode is explicit.

## Decision: Haskell MTS/WASM is the reference verifier direction

- **Decision**: The reference implementation should use the Haskell Merkle Tree
  Store as the proof substrate and expose verifier logic in browser contexts via
  WASM-compatible artifacts.
- **Rationale**: The repository was created because Haskell MTS now has WASM
  support. Browser verification is a major UX benefit for proof bundles carried
  between parties.
- **Alternatives considered**:
  - JavaScript-only verifier first: rejected because it would not exercise the
    MTS value proposition.
  - Server-only verifier: rejected because it moves trust back to an online
    service.

## Decision: Use JSON contracts for bundles and fixtures

- **Decision**: The first contracts are JSON Schema for proof bundles and
  certification facts plus an OpenAPI verifier contract for local/API testing.
- **Rationale**: EPCIS 2.0 has JSON/JSON-LD artifacts, and JSON fixtures are
  easy to validate across CLI, browser, and Haskell test harnesses.
- **Alternatives considered**:
  - CBOR-first bundles: deferred until the canonicalization and proof model are
    stable.
  - Schema-free fixtures: rejected because contract tests need explicit fields
    for roots, proofs, scopes, and outcomes.

## Official sources used

- GS1 EPCIS & CBV overview:
  https://www.gs1.org/standards/epcis
- GS1 EPCIS / CBV 2.0.0 artifacts:
  https://ref.gs1.org/standards/epcis/2.0.0/artefacts
- GS1 EPCIS Standard 2.0:
  https://ref.gs1.org/standards/epcis/2.0.0
- GS1 IP Policy:
  https://ref.gs1.org/gs1/ip-policy/
