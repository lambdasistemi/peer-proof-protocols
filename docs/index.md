# Peer Proof Protocols

This repository studies protocols where each peer authors its own
Merkle-authenticated state and shared outcomes are evaluated from exchanged
proof bundles rather than from a central truth service.

## Thesis

The relevant question is not "where can we put a Merkle tree?" It is:

- when does a protocol need signed messages, and when does it need signed state

This repository focuses on the second case:

- a peer must prove a fact was in its committed state
- another peer must acknowledge, dispute, or build on that fact from its own
  state
- a verifier should only need the relevant slices, not full databases

## What Belongs Here

- peer-owned facts
- per-peer roots
- inclusion and exclusion proofs
- proof bundles
- composition of verified bundles
- certification facts
- narrow witness or publication layers

## What Does Not Belong Here

- centralized oracle products
- SaaS overlays over GitHub, Linear, or similar tools
- protocols where plain signatures already solve the problem
- product UI exploration before protocol shape is stable

## How To Navigate

- Start with [Why Signed State](concepts/why-signed-state.md) if you want the
  core argument.
- Read [Inclusion, Exclusion, Completeness](concepts/inclusion-exclusion-completeness.md)
  for the proof vocabulary.
- Read [Composition and Certification](concepts/composition-and-certification.md)
  for the reusable protocol pattern.
- Read [On-Chain Root Anchoring](concepts/on-chain-root-anchoring.md) for how
  the trust layer changes when peer roots move to a ledger.
- Read [Anchor Identity And Claim History](concepts/anchor-identity-and-claim-history.md)
  for the Cardano UTxO discovery model.
- Read [Proof Request Language](concepts/proof-request-language.md) for the
  pull-based protocol between verifiers and proof holders.
- Read [Claim Authoring And Endorsement](concepts/claim-authoring-and-endorsement.md)
  for partial claims, scoring prompts, and endorsement requests.
- Use [Fit Rubric](concepts/fit-rubric.md) before spending time on a candidate.
- Browse [Candidates](candidates/index.md) for concrete targets.
- Active Spec Kit artifacts live in
  [specs/](https://github.com/lambdasistemi/peer-proof-protocols/tree/main/specs).
