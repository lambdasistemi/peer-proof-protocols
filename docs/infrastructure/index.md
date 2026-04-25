# Infrastructure Overview

The infrastructure proposal is deliberately layered.

PPP should not require every application to put all facts on-chain or route all
proofs through a registry. The useful split is:

```text
off-chain peers hold facts and proofs
public infrastructure anchors roots and ordering
verifiers check disclosed slices against those roots
```

## Layer Map

| Layer | Responsibility | Main Documentation |
|-------|----------------|--------------------|
| Peer facts | Canonical leaves authored by one peer | [Base Facts and Proof Bundles](../patterns/base-facts-and-proof-bundles.md) |
| Proof bundles | Portable inclusion and exclusion evidence | [Proof Request Language](../concepts/proof-request-language.md) |
| Certification facts | Reusable derived outcomes | [Composition and Certification](../concepts/composition-and-certification.md) |
| Trust model | Who is trusted for what | [Trust Model](../concepts/trust-model.md) |
| Publication layer | Timestamping, ordering, and discovery for roots | [On-Chain Root Anchoring](../concepts/on-chain-root-anchoring.md) |
| Claim-history anchors | Cardano UTxO shape for current root history | [Anchor Identity And Claim History](../concepts/anchor-identity-and-claim-history.md) |

## Off-Chain First

The base system works with signed roots and proof bundles:

- peers author facts
- peers compute roots
- holders disclose selected facts and proofs
- verifiers check the bundle locally

This is enough for many bilateral or low-dispute flows. The weakness is
timestamping and public non-equivocation: a peer can sign roots, but a verifier
may not know when those roots became public or whether different counterparties
were shown different roots.

## On-Chain When Freshness Matters

Moving roots on-chain changes the publication layer:

- roots get public ordering
- deadline and freshness checks can refer to ledger positions
- equivocation becomes harder to hide
- consuming smart contracts can use anchor UTxOs as reference inputs

The chain still does not know whether hidden facts are true. It only commits to
root history and enforces publication rules.

## Proof Transport

Proof distribution should be pull-oriented:

```text
requester already knows claim C
requester asks holder for current proof of C
holder returns a scoped proof bundle or a structured refusal/status
verifier checks the bundle against signed or anchored roots
```

The transport can be email, browser paste, wallet message, HTTP, DIDComm-like
channel, QR handoff, or application-specific API. PPP defines the evidence
contract, not the only transport.

## Cardano Shape

The current Cardano design uses one canonical anchor per:

```text
owner public key + namespace + scope
```

A unique anchor token identifies the current UTxO for that claim history.
The validator preserves the token, enforces owner-controlled monotonic updates,
and commits to an append-only history of claim roots.

That gives smart contracts a stable reference input for current peer state
without putting private facts on-chain.

## Open Infrastructure Questions

The next infrastructure research should answer:

- exact anchor-token derivation and minting policy
- key rotation and recovery model
- off-chain proof storage and request routing
- proof bundle canonical encoding and versioning
- browser verifier UX for pasted requests and responses
- how smart contracts consume proof bundles within size and cost limits
- when metadata-only publication is enough versus scripted anchors

