# Shared Subject

A `subject` is the thing peer-owned facts are *about*. The pattern that
makes PPP composable is choosing subjects that multiple peers can refer
to without coordinating: a content-addressed digest, a credential id, an
artifact hash, an article CID.

The point is not the hash function. The point is that the subject is a
public, immutable handle that any peer can attach facts to in its own
tree, and any verifier can pull facts about from many trees and
recompose them.

## The Pattern

```text
peer A:  fact_A1(subject S, ...)
         fact_A2(subject S, ...)
peer B:  fact_B1(subject S, ...)
peer C:  fact_C1(subject S, ...)
         excludes fact_C2(subject S, ...)
```

Each peer's facts live in its own signed tree. The verifier composes by
joining on `S`. No registry, no shared schema, no coordination.

## Why It Works

- `S` is content-addressed and immutable, so it cannot drift between
  peers.
- Each peer authors only the facts it owns, so authorship and authority
  stay clean.
- A verifier needs only the slices keyed by `S`, so disclosure stays
  minimal.
- The same `S` can attract more facts later without invalidating
  earlier ones.

## Already Used Across The Blueprints

The pattern is implicit in every blueprint, with the subject named by
domain:

- [Software Release Provenance](../protocols/software-release-provenance.md)
  uses an artifact digest.
- [Credential Status](../protocols/credential-status.md) uses a
  credential digest or credential id.
- [Endorsement With Revocation](../protocols/endorsement-with-revocation.md)
  uses a content digest, typically a CID.
- [Supply Chain Traceability](../protocols/supply-chain-traceability.md)
  uses a GS1 EPC or batch identifier.

Naming the pattern explicitly avoids reinventing it per blueprint.

## Independent Axes Over One Subject

A single subject can carry multiple orthogonal axes of facts. For
example, an article CID might attract:

```text
truth axis:    outlet:        published(CID, ...)
               fact-checker:  endorses(CID, policy P)
               outlet:        excludes retracted(CID)

access axis:   author:        issued_entitlement(reader R, CID, ...)
               author:        consumed(reader R, CID, epoch T)
               author:        excludes refunded(reader R, CID)
```

Both axes use the same join key. Neither needs to know about the other.
The reader app composes whichever axes it cares about.

This is the load-bearing reason PPP can layer integrity, access,
lifecycle, and policy facts independently over the same content.

## Relationship To The Anchor Concept

`subject` and `anchor` are different primitives.

- A `subject` is *what facts are about*. It is shared across peers.
- An `anchor` is *where one peer commits its claim history*. It is
  per-peer. See
  [Anchor Identity And Claim History](anchor-identity-and-claim-history.md).

A proof bundle for a fact about subject `S` cites the issuing peer's
anchor and the inclusion proof of `fact(S, ...)` in that peer's
committed root. Many peers' anchors can carry facts about the same `S`.

## Limits

The pattern does not solve:

- `subject discovery`: how a verifier learns the subject in the first
  place is out of scope.
- `subject identity binding`: linking a CID to a real-world thing
  (article, product, release) is solved by the layer that publishes
  the subject, not by PPP.
- `subject privacy`: knowing which subject a verifier is asking about
  reveals interest. Privacy-preserving lookup is a separate concern.

## See Also

- [Composition and Certification](composition-and-certification.md)
- [Inclusion, Exclusion, Completeness](inclusion-exclusion-completeness.md)
- [Anchor Identity And Claim History](anchor-identity-and-claim-history.md)
