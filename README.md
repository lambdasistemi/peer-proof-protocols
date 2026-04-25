# Peer Proof Protocols

Peer Proof Protocols is a spec-driven research proposal for applications where
each peer authors its own Merkle-authenticated state, exchanges scoped proof
bundles, and can promote verified bundles into reusable certified facts.

The value proposition is independent verification without turning one registry,
platform, or oracle into the owner of everyone else's truth.

## What PPP Adds

PPP is useful when a verifier needs to know more than "Alice signed this
message once."

It targets questions like:

- is this known claim still supported by Alice's current committed state?
- can Alice prove that no relevant revocation, dispute, hold, or supersession
  exists in the declared scope?
- can a third party verify the needed slice without receiving the full peer
  database?
- can a verified bundle become a certification fact that downstream users can
  either trust directly or audit through?

## Research Scope

In scope:

- peer-owned facts and per-peer roots
- inclusion, exclusion, completeness, freshness, and revocation semantics
- portable proof bundles exchanged between peers and verifiers
- claim authoring, endorsement, scoring, and proof-request flows
- composition of verified bundles into higher-order certified facts
- witness or publication layers that stay narrow and non-authoritative
- protocol evaluation, specifications, verifier rules, and infrastructure
  sketches

Out of scope:

- centralized oracle products
- SaaS trust overlays and registry-style applications
- protocols where detached signatures are already sufficient
- official conformance, accreditation, title-transfer, or compliance authority
  unless a separate governance path is explicit
- polished product UX before the protocol and trust model are clear

## Current Candidate Families

- supply chain and trade:
  `GS1 EPCIS`, `DCSA`, `Peppol`, `SWIFT documentary credits`
- software provenance and credentials:
  `in-toto`, `W3C VC Status Lists`, `OpenID4VC`, `Hyperledger AnonCreds`,
  `Hyperledger Aries`
- governance and protocol families:
  `Cardano governance`, `compliance audit-signoff`, `dispute resolution`
- internal sketches:
  `software provenance`, `milestone settlement`, `custody handoff`,
  `credential lifecycle`

## Documentation

Start with the published proposal:

- GitHub Pages: https://lambdasistemi.github.io/peer-proof-protocols/
- Value proposition:
  https://lambdasistemi.github.io/peer-proof-protocols/proposal/value-proposition/
- Protocol pipeline:
  https://lambdasistemi.github.io/peer-proof-protocols/protocols/
- Infrastructure overview:
  https://lambdasistemi.github.io/peer-proof-protocols/infrastructure/

Local source layout:

- `docs/proposal/`: value proposition and research method
- `docs/concepts/`: proof and trust model concepts
- `docs/patterns/`: reusable protocol patterns
- `docs/protocols/`: research pipeline
- `docs/candidates/`: candidate protocol analyses and sketches
- `docs/infrastructure/`: infrastructure map
- `docs/worksheets/`: evaluation worksheet

## Workflow

This repo uses Spec Kit for spec-driven development:

1. Establish or amend the constitution.
2. Write a feature specification.
3. Produce a plan and supporting research.
4. Generate tasks.
5. Implement only after the protocol and verification model are specified.

Project governance lives in `.specify/memory/constitution.md`.

Active peer-only feature specs:

- `specs/002-gs1-epcis-peer-traceability/`
- `specs/003-in-toto-peer-provenance/`

The earlier `specs/001-github-approval-receipts/` work is kept as historical
context for the oracle/registry direction that this repository moved away from.

## Local Preview

```bash
nix develop github:paolino/dev-assets?dir=mkdocs --quiet -c mkdocs serve
```

Pull request previews are deployed to:

```text
https://lambdasistemi-peer-proof-protocols-pr-PR_NUMBER.surge.sh
```
