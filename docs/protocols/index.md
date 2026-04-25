# Protocol Research Pipeline

The protocol pipeline turns the value proposition into concrete research
targets.

PPP should only move a candidate forward when the candidate has a clear shared
claim, multiple peers with distinct authority, and a verifier who benefits from
checking a selective proof bundle.

## Active Specs

These are the current peer-only feature specs:

- [GS1 EPCIS Peer Traceability Proofs](https://github.com/lambdasistemi/peer-proof-protocols/blob/main/specs/002-gs1-epcis-peer-traceability/spec.md)
- [in-toto Peer Provenance Proofs](https://github.com/lambdasistemi/peer-proof-protocols/blob/main/specs/003-in-toto-peer-provenance/spec.md)

The GitHub approval receipts spec in `specs/001-*` is retained as historical
context. It belongs to the older oracle/registry analysis, not the current
peer-only target.

## Candidate Classes

The current research set has three classes.

### Standards-backed protocols

These have public specifications or public interoperability material:

- [GS1 EPCIS](../candidates/gs1-epcis/index.md)
- [DCSA Track and Trace](../candidates/dcsa-track-and-trace/index.md)
- [DCSA Bill of Lading](../candidates/dcsa-bill-of-lading/index.md)
- [Peppol Post-Award](../candidates/peppol-post-award/index.md)
- [Peppol Pre-Award](../candidates/peppol-pre-award/index.md)
- [SWIFT Documentary Credits](../candidates/swift-documentary-credits/index.md)
- [in-toto](../candidates/in-toto/index.md)
- [W3C VC Status Lists](../candidates/w3c-vc-status-lists/index.md)
- [OpenID4VC](../candidates/openid4vc/index.md)
- [Hyperledger AnonCreds](../candidates/hyperledger-anoncreds/index.md)
- [Hyperledger Aries](../candidates/hyperledger-aries/index.md)
- [Cardano Governance](../candidates/cardano-governance/index.md)

### Protocol families

These are useful domains but not one externally governed protocol:

- [Compliance Audit-Signoff](../candidates/compliance-audit-signoff/index.md)
- [Dispute Resolution](../candidates/dispute-resolution/index.md)

### Internal sketches

These are pattern sketches used to test the fit rubric and user stories:

- [Software Provenance](../candidates/software-provenance/index.md)
- [Milestone Settlement](../candidates/milestone-settlement/index.md)
- [Custody Handoff](../candidates/custody-handoff/index.md)
- [Credential Lifecycle](../candidates/credential-lifecycle/index.md)

They should not be presented as researched external standards until they have
source-backed protocol notes.

## From Candidate To Spec

A candidate becomes a spec only when it has:

- named peers with disjoint fact authority
- one high-value shared claim
- required positive and negative facts
- a proof bundle shape
- a current-state or freshness requirement
- a safe software role
- a documented infrastructure path

The spec should then define verifier behavior first. Implementation follows
only after the proof and trust model are precise enough to test.

