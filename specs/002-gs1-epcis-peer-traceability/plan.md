# Implementation Plan: GS1 EPCIS Peer Traceability Proofs

**Branch**: `002-gs1-epcis-peer-plan` | **Date**: 2026-04-24 | **Spec**:
[spec.md](spec.md)

**Input**: Feature specification from
`/specs/002-gs1-epcis-peer-traceability/spec.md`

**Note**: This plan covers Phase 0 research and Phase 1 design. It does not
create implementation tasks yet.

## Status

**Completed**: Feature specification, Phase 0 research decisions, Phase 1
protocol data model, proof-bundle contract, verifier contract, and quickstart
walkthrough

**Current**: Ready for `/speckit.tasks`

**Blockers**: None

## Summary

Define a peer-only proof layer for GS1 EPCIS-style traceability. Each
organization maps its own visibility events, statuses, and exceptions into
canonical peer facts, commits those facts to its own Merkle-authenticated
state, and exchanges portable proof bundles for bounded shipment, batch, asset,
or trade-item claims. Verifiers check peer roots, inclusion proofs, scoped
exclusion evidence, freshness, and optional certification facts without
trusting a central registry or aggregator.

The first design target is a bounded inbound traceability segment involving at
least supplier and logistics-provider roots, with optional receiver and
certifier roots. The reference verification path should be usable from CLI and
browser contexts, with Haskell MTS as the intended Merkle substrate and WASM as
the browser verification delivery path once implementation begins.

## Technical Context

**Protocol / Domain**: GS1 EPCIS-style supply-chain traceability and visibility
events

**Peer Set / Roles**: Supplier or product owner, logistics provider or
warehouse, receiver or retailer, optional auditor, insurer, customs actor, or
certifier

**Peer-Owned Facts**: Commissioning, packing, shipping, receiving, custody
handoff, inspection, aggregation, deaggregation, transformation, disposition,
hold, release, recall, rejection, exception, dispute, destruction, and
certification facts asserted by the peer that observed or issued them

**Shared Claim / Outcome**: A disclosed traceability segment for a shipment,
batch, asset, or trade item is supported by the named peer roots, is fresh
enough for policy P, has no proven blocking condition in the declared scope,
and satisfies any declared certification rule

**Proof-Carrying Message**: Traceability proof bundle containing disclosed
facts, inclusion proofs, exclusion or completeness evidence, peer root
checkpoints, peer identity references, vocabulary versions, policy metadata,
and optional certification facts

**Composition Model**: Base peer facts -> verified traceability bundle ->
derived claim -> certification fact in the certifier peer tree

**Audit Mode**: Mixed. Downstream verifiers may `trust certifier` for the
certification fact alone or `audit through` to the pinned input proofs and peer
roots

**Verification Surface**: CLI and browser verifier are both in scope for the
reference implementation; the browser path should use WASM-compatible verifier
logic

**Witness / Publication Layer**: Optional timestamp or witness publication for
availability only; witnesses are not semantic truth owners

**Merkle Substrate**: Per-peer namespaces and roots using Haskell MTS/MPF-style
authenticated maps. The design requires inclusion proofs and scoped exclusion
or explicit unsupported-exclusion outcomes

**Implementation Stack**: Reference implementation target is Haskell for the
Merkle/proof core and verifier logic, WASM for browser verification, plus JSON
schemas and golden fixtures for cross-runtime interoperability

**Storage / Anchoring**: Each peer stores its own source events, canonical
facts, roots, and proof material. Bundles are portable files or messages.
External anchoring is optional and non-authoritative

**Testing Strategy**: Contract validation, golden canonicalization fixtures,
proof replay tests, negative verification tests, cross-peer consistency tests,
exclusion tests, certification audit-through tests, and CLI/browser parity
tests

**Constraints**: Do not redistribute GS1 standards text, do not claim GS1
conformance or endorsement, do not centralize semantic truth, expose stale
roots and unsupported exclusions, preserve selective disclosure, and make
remaining real-world trust assumptions explicit

**Scale / Scope**: MVP supports one bounded traceability segment with two to
four peers, tens of disclosed facts, one or more roots per peer, and one
optional certification fact per verified bundle

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- [x] The feature targets a peer-only protocol, not a centralized oracle or
      coordinator product.
- [x] Peer boundaries are explicit: what each peer authors, signs, stores, and
      proves is documented.
- [x] Each peer has its own root lifecycle and proof publication model.
- [x] The design explains why authenticated state and Merkle proofs add value
      beyond detached signatures.
- [x] Canonicalization, ordering, normalization, and versioning are defined
      well enough to reproduce identical leaves, roots, and proof bundles from
      identical peer inputs.
- [x] A proof-carrying interaction and an independent partial-verification path
      are defined.
- [x] If the feature promotes derived claims into certified facts, the
      certification payload, policy/rule identifiers, and downstream audit mode
      are defined.
- [x] Any witness, relay, or settlement layer is narrow, passive, and
      non-authoritative.
- [x] Key custody, freshness limits, dispute modes, and degraded behavior are
      explicitly documented.
- [x] Golden fixtures and negative verification cases are planned for each new
      or changed cross-peer claim.

## Project Structure

### Documentation (this feature)

```text
specs/002-gs1-epcis-peer-traceability/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   ├── traceability-proof-bundle.schema.json
│   ├── certification-fact.schema.json
│   └── verifier-api.yaml
└── tasks.md
```

### Source Code (repository root)

```text
protocols/
└── gs1-epcis-traceability/
    ├── canonical/
    ├── rules/
    ├── messages/
    └── verification/
peers/
└── traceability/
    ├── roots/
    ├── proofs/
    ├── exclusions/
    └── certifications/
schemas/
├── canonical/
├── bundles/
└── certifications/
verifiers/
├── cli/
├── browser/
└── shared/
tests/
├── contract/
├── golden/
├── replay/
├── integration/
└── wasm-parity/
```

**Structure Decision**: Keep this repository protocol-first. The plan defines
schemas, verifier contracts, data model, fixtures, and reference verifier
shape. Implementation tasks should add code only after the task phase confirms
the Haskell MTS/WASM integration boundary and fixture format.

## Complexity Tracking

No constitution violations require justification in this plan.

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| None | N/A | N/A |
