# Peer Proof Protocols

Peer Proof Protocols is a spec-driven research repository for peer-only
protocols where each peer authors its own Merkle-authenticated state, exchanges
proof bundles, and can promote verified bundles into certified facts.

## In Scope

- peer-owned facts and per-peer roots
- inclusion, exclusion, and completeness-style questions
- portable proof bundles exchanged between peers and verifiers
- composition of verified bundles into higher-order certified facts
- witness or publication layers that stay narrow and non-authoritative
- protocol evaluation, specifications, and verification rules

## Out of Scope

- centralized oracle products
- SaaS trust overlays and registry-style applications
- protocols where detached signatures are already sufficient
- polished product UX before the protocol and trust model are clear

## When This Repo Is A Good Fit

This repository is a good fit when most of the following are true:

- the outcome depends on facts from two or more peers
- each peer owns a different part of the truth
- state evolves over time and historical roots matter
- verifiers usually need only a slice of each peer's state
- negative claims matter, such as "not revoked" or "not disputed"
- derived certifications become reusable facts in later steps

## Current Candidate Families

- supply chain and trade:
  `GS1 EPCIS`, `DCSA`, `Peppol`, `SWIFT documentary credits`
- software provenance and credentials:
  `in-toto`, `W3C VC Status Lists`, `OpenID4VC`, `Hyperledger AnonCreds`,
  `Hyperledger Aries`
- governance and protocol families:
  `Cardano governance`, `compliance audit-signoff`, `dispute resolution`

## Workflow

This repo uses Spec Kit for spec-driven development:

1. Establish or amend the constitution.
2. Write a feature specification.
3. Produce a plan and supporting research.
4. Generate tasks.
5. Implement only after the protocol and verification model are specified.

Project governance lives in `.specify/memory/constitution.md`.

Active feature specifications live in `specs/`. Current peer-only PPP specs
include `specs/002-gs1-epcis-peer-traceability/` and
`specs/003-in-toto-peer-provenance/`.

## Documentation

- Repository overview: `docs/index.md`
- Core concepts: `docs/concepts/`
- Reusable protocol patterns: `docs/patterns/`
- Candidate protocol analyses: `docs/candidates/`
  Start at `docs/candidates/index.md`.
- Reusable evaluation worksheet: `docs/worksheets/protocol-evaluation-sheet.md`
- GitHub Pages site:
  `https://lambdasistemi.github.io/peer-proof-protocols/`
- Pull request previews:
  `https://lambdasistemi-peer-proof-protocols-pr-<number>.surge.sh`
  when `SURGE_TOKEN` is configured for the repository

To preview the documentation locally:

```bash
nix develop github:paolino/dev-assets?dir=mkdocs --quiet -c mkdocs serve
```
