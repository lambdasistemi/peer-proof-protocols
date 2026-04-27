# Independent Journalism

Independent journalism is a protocol family rather than a single external
standard. Outlets, fact-checkers, sources, archives, and press-freedom NGOs
each author distinct claims about the same body of reporting, and readers,
courts, and platforms need to compose those claims into something they can
trust.

## Fit

This is a strong Merkle candidate because the trustworthiness of a story is
inherently composed: an outlet publishes, a source corroborates, a
fact-checker reviews, an archive timestamps, and an NGO endorses. Each peer
owns its own claims, and the verifier needs both the included endorsements
and the absence of retractions.

## Likely Software Role

- peer-side endorsement publisher for outlets, fact-checkers, sources, and
  press-freedom NGOs
- verifier and proof-bundle layer over content-addressed articles on IPFS,
  Arweave, or C2PA-stamped media
- certification layer that promotes audited bundles into reusable trust
  facts such as `passed independent fact-check policy P`
- adapter between content-addressed storage and PPP claim history without
  acting as an editorial authority

## IP / License Signals

Risk level: `Low`

- There is no protocol owner to license from. Independent journalism is a
  domain, not an externally governed standard.
- Adjacent vocabularies we are likely to interoperate with, such as
  `schema.org/ClaimReview`, IPTC photo metadata, and C2PA manifests, are
  permissive and implementation-friendly.
- Trademark risk is at the level of named outlets and named fact-checkers,
  not the protocol.

## Main Non-License Risk

The dominant operational risk is becoming a centralized arbiter of
journalistic legitimacy. If one PPP-shaped product becomes the default
endorsement registry, we have reproduced the NewsGuard problem we set out
to avoid.

The protocol must enforce:

- bundles are auditable end-to-end, with every promoted fact tracing back
  to a peer's signed root and its inclusion or exclusion proofs
- no PPP component plays the role of single source of truth; the certifier
  is just another peer
- revocation is a first-class operation, so corrections, retractions, and
  source repudiations are part of the protocol rather than out-of-band
  metadata

A secondary risk is scope confusion. PPP does not solve the economic crisis
of journalism. It gives independent reporting a verifiable trust substrate
that does not depend on platforms; it does not pay reporters or fix
distribution.

Identity bootstrapping is deliberately out of scope. Linking an outlet's
peer key to its real-world identity is solved by DNS-based discovery,
`.well-known` files, or W3C DIDs published on the outlet's own domain. The
protocol does not try to own that step.

## Take

This is one of the strongest non-software candidates in the set, on a par
with `GS1 EPCIS` for politically visible value. The clean role is a
peer-side endorsement and verification toolkit plus the
[Endorsement With Revocation](../../protocols/endorsement-with-revocation.md)
blueprint. We should not operate the registry, run the fact-check authority,
or rank outlets.

## Sources

- [Endorsement With Revocation blueprint](../../protocols/endorsement-with-revocation.md)
- [Composition and Certification](../../concepts/composition-and-certification.md)
- [Audit Through vs Trust Certifier](../../patterns/audit-through-vs-trust-certifier.md)
- [Fit Rubric](../../concepts/fit-rubric.md)
- [C2PA specification](https://c2pa.org/specifications/specifications/2.0/index.html)
- [schema.org ClaimReview vocabulary](https://schema.org/ClaimReview)
- [Journalism Trust Initiative (RSF)](https://www.journalismtrustinitiative.org/)
