# Research: in-toto Peer Provenance Proofs

## Decision: Wrap in-toto evidence; do not replace in-toto

- **Decision**: PPP canonical facts are derived from in-toto layouts, links,
  statements, predicates, envelopes, and bundles, but PPP does not redefine
  those objects.
- **Rationale**: in-toto already provides the software supply-chain model:
  project owners define layouts, authorized functionaries produce signed link
  metadata, and verifiers validate the final product against the layout. The
  attestation framework also defines statements, predicates, envelopes, and
  bundles for verifiable claims.
- **Alternatives considered**:
  - Define a competing provenance protocol: rejected because it duplicates
    in-toto and weakens interoperability.
  - Treat in-toto as only inspiration: rejected because in-toto's existing
    metadata ecosystem is the adoption path.

## Decision: Start with artifact-release provenance

- **Decision**: The first claim type is `ppp.in_toto.provenance_bundle.v1` for
  one artifact digest and one declared policy.
- **Rationale**: Artifact release is narrow enough to fixture and broad enough
  to show multi-peer value across owner, builder, tester, scanner, publisher,
  and certifier roots.
- **Alternatives considered**:
  - Full package ecosystem transparency: rejected because it introduces
    registry and log governance before the peer proof model is proven.
  - Single build-step proof: rejected because in-toto signatures already cover
    a one-step signed statement better than PPP does.

## Decision: Peer roots belong to functionaries and authorities

- **Decision**: Project owners, builders, scanners, publishers, and certifiers
  maintain independent roots over their own provenance facts.
- **Rationale**: in-toto already distinguishes owners and functionaries. PPP
  should preserve those authorship boundaries and let verifiers know which peer
  committed each fact.
- **Alternatives considered**:
  - A central provenance registry root: rejected because it turns PPP into an
    oracle product.
  - A release-manager tree over all received attestations: rejected because it
    hides the state and freshness of the original functionaries.

## Decision: Use Merkle state for freshness, exclusion, and selected disclosure

- **Decision**: Merkle proofs are required for peer state questions that
  detached attestation signatures do not answer: current inclusion,
  supersession, revocation, absence of blocking facts, and selective disclosure.
- **Rationale**: The in-toto attestation Bundle specification states that a
  bundle is not authenticated as a whole, and that deletion, replay, or
  injection risks must be considered. Peer roots provide a separate continuity
  and freshness layer around selected evidence.
- **Alternatives considered**:
  - Sign the final bundle only: rejected because it authenticates the packager's
    collection, not each peer's current state.
  - Require every verifier to query each peer live: rejected because delayed,
    offline, and customer-side verification should work from exported bundles.

## Decision: Negative supply-chain claims require committed indexes

- **Decision**: Claims such as "not revoked", "no active release hold", or "no
  critical vulnerability above threshold" verify only when the peer root
  semantics include the relevant committed key space.
- **Rationale**: Absence from a selected attestation bundle is not absence from
  peer state.
- **Alternatives considered**:
  - Treat omitted blocking attestations as clean: rejected because it rewards
    selective omission.
  - Make all exclusions mandatory in MVP: rejected because some peers may start
    with inclusion-only roots, provided unsupported exclusions are explicit.

## Decision: Avoid defining a new in-toto predicate initially

- **Decision**: PPP certification facts live at the PPP layer first. If later
  adoption needs an in-toto predicate, that should follow the in-toto predicate
  contribution process.
- **Rationale**: The attestation framework already encourages use of existing
  predicates where possible and supports new predicate types when necessary.
  PPP should avoid adding a predicate until the certification semantics are
  stable.
- **Alternatives considered**:
  - Create `ppp-certification` as an in-toto predicate immediately: deferred
    because the protocol semantics need review first.
  - Encode certification only as prose: rejected because downstream audit needs
    pinned inputs and machine-verifiable fields.

## Decision: Certification facts are reusable composed facts

- **Decision**: A certifier may publish
  `ppp.in_toto.certification.v1` after verifying a proof bundle and applying a
  named policy.
- **Rationale**: This lets downstream verifiers choose `trust certifier` or
  `audit through`, making composition explicit instead of hiding it in a
  dashboard result.
- **Alternatives considered**:
  - Mandatory audit-through for every consumer: rejected because many release
    and procurement flows intentionally delegate to certifiers.
  - Opaque certification labels: rejected because they cannot be replayed or
    audited against pinned inputs.

## Decision: Haskell MTS/WASM is the reference verifier direction

- **Decision**: Use Haskell MTS for the authenticated-state core and WASM for
  browser verification once implementation begins.
- **Rationale**: PPP exists to explore browser-verifiable Merkle proof bundles
  carried between parties. in-toto is a good software-first target for that
  path.
- **Alternatives considered**:
  - Python in-toto implementation only: rejected because it does not exercise
    MTS or browser verification.
  - Server-only verification: rejected because it makes the verifier trust a
    service instead of exported proof bundles.

## Decision: JSON contracts plus JSON Lines interoperability

- **Decision**: PPP bundles and certification facts use JSON Schema, while
  in-toto bundles remain interoperable as JSON Lines where applicable.
- **Rationale**: The in-toto Bundle layer uses JSON Lines for multiple
  attestations. PPP can carry references or selected evidence while validating
  its own proof-bundle envelope with JSON Schema.
- **Alternatives considered**:
  - CBOR-first proof bundles: deferred until canonicalization and fixture
    requirements are stable.
  - Schema-free proof bundles: rejected because verifier contracts require
    explicit roots, proofs, coverage, outcomes, and trust modes.

## Official sources used

- in-toto project README:
  https://github.com/in-toto/in-toto
- in-toto Python implementation documentation:
  https://in-toto.readthedocs.io/en/latest/
- in-toto Attestation Framework:
  https://github.com/in-toto/attestation
- in-toto Statement layer specification:
  https://raw.githubusercontent.com/in-toto/attestation/main/spec/v1/statement.md
- in-toto Envelope layer specification:
  https://raw.githubusercontent.com/in-toto/attestation/main/spec/v1/envelope.md
- in-toto Bundle layer specification:
  https://raw.githubusercontent.com/in-toto/attestation/main/spec/v1/bundle.md
- in-toto Attestation predicates:
  https://raw.githubusercontent.com/in-toto/attestation/main/spec/predicates/README.md
