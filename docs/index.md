# Peer Proof Protocols

Peer Proof Protocols is a research proposal for applications where each party
authors its own Merkle-authenticated state and shares only the facts and proofs
needed for a verifier to evaluate a claim.

The proposal is not "put a Merkle tree somewhere." The proposal is:

```text
turn peer-owned state into portable, selectively disclosed, independently
verifiable evidence
```

## Value Proposition

Many multi-party applications rely on a platform, registry, or operator to say
what happened. That is acceptable when one authority owns the truth. It is weak
when the outcome depends on facts held by several peers, negative claims such
as "not revoked" or "not disputed", and later audits or disputes.

PPP proposes a narrower trust layer:

- each peer commits to its own state
- each verifier receives only the relevant facts and proofs
- proof bundles can include both inclusion and exclusion evidence
- verified bundles can be promoted into reusable certification facts
- public witnesses or blockchains can anchor roots without becoming the source
  of semantic truth

The result should be a browser, wallet, CLI, API, or smart contract verifier
that can check a received proof bundle without asking a central service to
interpret the whole application.

## Research Thesis

Signed messages are enough when the question is "did Alice say this once?"

Signed state is valuable when the question is closer to:

```text
does Alice's current committed state still support this known claim,
and can Alice prove no blocking fact exists in the relevant scope?
```

The repository focuses on protocols that are:

- `multi-peer`: several actors own necessary facts
- `stateful`: facts evolve and old roots still matter
- `partial`: verifiers need slices, not full databases
- `composable`: verified bundles can become certified facts

## Proposal Boundary

This is a proposal and research program, not a finished product claim.

In scope:

- peer-owned facts and per-peer roots
- proof request and proof bundle language
- inclusion, exclusion, completeness, freshness, and revocation semantics
- certification facts and audit-through modes
- narrow publication layers, including on-chain root anchoring
- candidate protocol analysis grounded in public APIs or standards

Out of scope:

- centralized oracle products
- SaaS overlays where one operator owns the only meaningful state
- claims that Merkle roots make application facts objectively true
- official conformance, accreditation, legal title transfer, or regulatory
  authority unless a separate governance path exists

## How To Browse

1. Start with the [Value Proposition](proposal/value-proposition.md) for the
   business and trust argument.
2. Read the [Research Method](proposal/research-method.md) to understand how
   candidates are scored and filtered.
3. Read [Why Signed State](concepts/why-signed-state.md) and
   [Inclusion, Exclusion, Completeness](concepts/inclusion-exclusion-completeness.md)
   for the core proof vocabulary.
4. Read the [Protocol Research Pipeline](protocols/index.md) and
   [Candidate Protocols](candidates/index.md) for concrete targets.
5. Read [Infrastructure Overview](infrastructure/index.md) for the path from
   off-chain proof bundles to on-chain claim-history anchors.

## Current Research Outputs

The active peer-only specs are:

- [GS1 EPCIS Peer Traceability Proofs](https://github.com/lambdasistemi/peer-proof-protocols/blob/main/specs/002-gs1-epcis-peer-traceability/spec.md)
- [in-toto Peer Provenance Proofs](https://github.com/lambdasistemi/peer-proof-protocols/blob/main/specs/003-in-toto-peer-provenance/spec.md)

The first reusable protocol blueprints are:

- [Supply Chain Traceability](protocols/supply-chain-traceability.md)
- [Software Release Provenance](protocols/software-release-provenance.md)
- [Credential Status](protocols/credential-status.md)
- [Milestone Settlement](protocols/milestone-settlement.md)

The earlier GitHub approvals work remains in `specs/001-*` as historical
context. It represents the oracle/registry direction that this repository moved
away from.
