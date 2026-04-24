# Cardano Governance

Cardano governance under `CIP-1694` and related CIPs combines on-chain voting
and ratification with off-chain documents, metadata, and discussion artifacts.

## Fit

This is a strong certification-facts candidate. Proposals, metadata, votes,
ratification, and constitution anchors create a clear ladder from base facts to
higher-order governance outcomes.

## Likely Software Role

- governance client or wallet
- verifier for proposal, vote, and metadata bundles
- metadata tooling and explorer support
- sidecar proof system for off-chain discussion, evidence, or certifications

## IP / License Signals

Risk level: `Low`

- `CIP-1694` is published under `CC-BY-4.0`.
- `CIP-100` is also `CC-BY-4.0`.
- That makes implementation and documentation work comparatively low-friction.

## Main Non-License Risk

- legitimacy comes from community adoption, not from the protocol text alone
- `CIP-1694` explicitly leaves key off-chain governance conventions out of scope

## Take

This is a strong research target for certification ladders, but it is not a
purely off-chain peer protocol. It sits partly inside a chain-governed system.

## Sources

- [CIP-1694](https://cips.cardano.org/cip/cip-1694)
- [CIP-100](https://cips.cardano.org/cips/cip100)
