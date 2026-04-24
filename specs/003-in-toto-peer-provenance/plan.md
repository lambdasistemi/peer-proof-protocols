# Implementation Plan: in-toto Peer Provenance Proofs

**Branch**: `003-in-toto-peer-provenance` | **Date**: 2026-04-24 | **Spec**:
[spec.md](spec.md)

**Input**: Feature specification from
`/specs/003-in-toto-peer-provenance/spec.md`

**Note**: This plan covers Phase 0 research and Phase 1 design. It does not
create implementation tasks yet.

## Status

**Completed**: Feature specification, Phase 0 research decisions, Phase 1
protocol data model, proof-bundle contract, certification contract, verifier
contract, and quickstart walkthrough

**Current**: Ready for review, then `/speckit.tasks`

**Blockers**: None

## Summary

Define a peer-only proof layer for in-toto provenance. Existing in-toto layouts,
links, statements, predicates, envelopes, and bundles remain the source
attestation model. PPP adds per-peer authenticated state so a verifier can
check which peer committed each fact, whether selected attestations are fresh
under peer roots, whether blocking facts are absent inside a committed scope,
and whether a higher-order policy decision was certified with pinned inputs.

The first design target is a bounded artifact-release provenance bundle
involving at least project-owner and functionary roots, with optional scanner,
publisher, and certifier roots. The reference verification path should be
usable from CLI and browser contexts, with Haskell MTS as the intended Merkle
substrate and WASM as the browser verification delivery path once
implementation begins.

## Technical Context

**Protocol / Domain**: in-toto software supply-chain provenance and attestation
evidence

**Peer Set / Roles**: Project owner, source maintainer, builder, tester,
scanner, packager, publisher, release manager, downstream verifier, and
certifier

**Peer-Owned Facts**: Layout and policy facts, source and review facts, build
provenance facts, test result facts, SBOM and vulnerability facts, release
publication facts, revocation facts, exception facts, key rotation facts, and
certification facts authored by the peer responsible for each statement

**Shared Claim / Outcome**: An artifact digest satisfies policy P because the
required peer-authored evidence is present, fresh, consistent, and not blocked
by proven active revocation, failed test, vulnerability threshold breach,
compromised-key notice, release hold, or policy exception

**Proof-Carrying Message**: Provenance proof bundle containing selected in-toto
evidence, canonical peer facts, inclusion proofs, exclusion or completeness
evidence, peer root checkpoints, trust-anchor references, policy metadata,
coverage limits, and optional certification facts

**Composition Model**: Base in-toto evidence -> canonical peer facts -> verified
provenance proof bundle -> derived policy result -> certification fact in the
certifier peer tree

**Audit Mode**: Mixed. Downstream verifiers may `trust certifier` for a pinned
policy result or `audit through` to the input peer roots and in-toto evidence

**Verification Surface**: CLI and browser verifier are both in scope for the
reference implementation; browser verification should use WASM-compatible
verifier logic

**Witness / Publication Layer**: Optional transparency log, package registry,
or timestamp witness for availability only; witnesses are not semantic truth
owners

**Merkle Substrate**: Per-peer namespaces and roots using Haskell MTS/MPF-style
authenticated maps. The design requires inclusion proofs and scoped exclusion
or explicit unsupported-exclusion outcomes

**Implementation Stack**: Reference implementation target is Haskell for the
Merkle/proof core and verifier logic, WASM for browser verification, JSON
contracts for bundles, and JSON Lines interoperability for in-toto bundles

**Storage / Anchoring**: Each peer stores its own attestations, canonical facts,
roots, revocation state, and proof material. Bundles are portable files or
messages. External anchoring is optional and non-authoritative

**Testing Strategy**: Contract validation, golden canonicalization fixtures,
in-toto fixture replay, proof replay tests, negative verification tests,
exclusion tests, certification audit-through tests, and CLI/browser parity
tests

**Constraints**: Do not redefine in-toto, do not require a central registry, do
not imply signed attestations prove software safety, expose stale roots and
unsupported exclusions, preserve selective disclosure, and make remaining
trust assumptions explicit

**Scale / Scope**: MVP supports one artifact digest, one policy, two to five
peers, tens of disclosed facts, one or more roots per peer, and one optional
certification fact per verified bundle

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
specs/003-in-toto-peer-provenance/
├── spec.md
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   ├── provenance-proof-bundle.schema.json
│   ├── certification-fact.schema.json
│   └── verifier-api.yaml
└── tasks.md
```

### Source Code (repository root)

```text
protocols/
└── in-toto-provenance/
    ├── canonical/
    ├── rules/
    ├── messages/
    └── verification/
peers/
└── provenance/
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

**Structure Decision**: Keep the repository protocol-first. The plan defines
schemas, verifier contracts, data model, fixtures, and reference verifier
shape. Implementation tasks should add code only after the task phase confirms
the Haskell MTS/WASM integration boundary and in-toto fixture format.

## Complexity Tracking

No constitution violations require justification in this plan.

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| None | N/A | N/A |
