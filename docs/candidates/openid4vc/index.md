# OpenID4VC

OpenID4VC is the transport and interaction layer around verifiable credential
issuance and presentation flows, especially `OID4VCI` and `OID4VP`.

## Fit

This is not the root Merkle protocol by itself. It is better understood as a
transport and interoperability layer that could carry credentials, disclosures,
or certifications built on top of peer-authored state.

## Likely Software Role

- wallet, issuer, or verifier integration layer
- transport for proof bundles and certified facts
- profile or adapter around lower-level credential or Merkle-state systems

## IP / License Signals

Risk level: `Low`

- OpenID4VCI and OpenID4VP are Final OpenID specifications.
- The OpenID final-spec notices grant copyright permission for implementation
  work and point to the OpenID patent promise process.
- This is a comparatively implementation-friendly environment.

## Main Non-License Risk

- profile churn and interoperability details
- dependence on underlying credential formats and wallet behavior
- the risk of doing a lot of envelope work without moving the peer-state problem

## Take

Useful as an outer transport layer, but not where the Merkle novelty lives. It
is likely something we integrate with, not the primary candidate we center the
repo on.

## Sources

- [OpenID for Verifiable Credential Issuance 1.0](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html)
- [OpenID for Verifiable Presentations 1.0](https://openid.net/specs/openid-4-verifiable-presentations-1_0-final.html)
