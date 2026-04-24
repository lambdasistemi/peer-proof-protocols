# DCSA Bill of Lading

DCSA Bill of Lading standardizes digital bill-of-lading data and is now paired
with an interoperability framework for electronic bills of lading across
platforms.

## Fit

This is a strong Merkle candidate because issuance, transfer, acceptance,
surrender, and amendment involve multiple parties with distinct authority over
their own facts.

## Likely Software Role

- bill-of-lading state engine for one participant or platform
- verifier for issuance, transfer, and surrender bundles
- proof export layer for legal or operational handoffs
- internal interoperability adapter, not the global control authority

## IP / License Signals

Risk level: `Medium`

- The technical standards are published openly enough to implement against.
- The tricky part is not the API text itself; it is the surrounding
  interoperability framework and branded participation claims.

## Main Non-License Risk

- interoperable eBL requires more than message syntax
- DCSA's interoperability model includes a legal framework, a control tracking
  registry, and standardized provider relationships
- if we act like a full eBL platform or title-control network, the legal burden
  rises sharply

## Take

This is a strong but higher-friction candidate. The safest role is software for
one participant or platform, not an unauthorized cross-platform control or
interoperability service.

## Sources

- [Bill of Lading standard | DCSA](https://dcsa.org/standards/bill-of-lading)
- [eBL platform interoperability | DCSA](https://dcsa.org/our-mission/ebl-interoperability-platform)
