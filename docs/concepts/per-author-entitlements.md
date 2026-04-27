# Per-Author Cryptographic Entitlements

This concept note explores whether PPP can underpin a per-author
paid-access model for content-addressed media: bundles of decryption
rights bought directly from an author, without a platform intermediary.

It is brainstorm-stage, not a settled design. Several architectural
decisions are deliberately left open.

## Motivation

Per-publication subscriptions are unpopular but per-author subscriptions
work (Substack). Per-article paywalls have a long failure history
(Blendle, Inkl, Scroll). The interesting question is whether a
*cryptographic, platform-independent, per-author bundle* — a reader
buys the right to decrypt N articles from one author — can avoid the
failure modes of per-article paywalls while keeping the per-author
ownership story.

What a protocol-shaped version can offer that platforms cannot:

- the author owns the keys and the entitlement issuance, not a platform
- the entitlement is portable across reader apps
- consumption is anonymous to the author when desired
- refund and revocation are protocol primitives, not customer-service
  tickets

Whether this becomes a *business* depends on solving discovery, which a
protocol does not solve.

## Two Axes Over One Subject

Each article has a single content-addressed subject (typically an IPFS
CID for ciphertext). Two independent axes of PPP facts attach to that
subject. See [Shared Subject](shared-subject.md).

```text
truth axis (L1a)
  outlet:        published(CID, ...)
  fact-checker:  endorses(CID, policy P)
  archive:       archived(CID, timestamp T)
  outlet:        excludes retracted(CID)

access axis (L1b)
  author:        issued_entitlement(reader R, bundle B, n_remaining N)
  author:        consumed(reader R, CID, epoch T)
  author:        excludes refunded(reader R, bundle B)
  author:        excludes entitlement_revoked(bundle B)
```

The two axes are orthogonal. The truth axis is the
[Independent Journalism candidate](../candidates/independent-journalism/index.md)
and the [Endorsement With Revocation blueprint](../protocols/endorsement-with-revocation.md).
The access axis is what this note explores.

## The Reading Flow

```text
1. reader sees CID and headline (public)
2. reader queries truth-axis facts (PPP, free, no payment)
   - any retraction or supersession?
   - which fact-checkers endorse?
   - which curated bundles include?
3. reader decides whether to consume a credit
4. reader presents entitlement proof to a key-delivery service
5. service issues a proxy re-encryption (PRE) delegation for CID
6. reader decrypts cleartext locally
7. author publishes consumed(reader R, CID, epoch T) in its tree
```

Step 2 is the pre-decision support layer that the per-article paywall
era did not have. Steps 4–6 are out of PPP scope.

## What Is Outside PPP

PPP holds *facts*. It does not hold keys, run consensus, or move money.
The non-PPP pieces are:

- `enforcement`: proxy re-encryption keepers, threshold key custody, or
  publisher-side key delivery
- `payment rail`: on-chain micropayments, native-asset mint at issuance,
  Lightning, or fiat — whichever rail emits proofs the issuance side
  can consume
- `discovery`: how the reader finds articles in the first place

PPP records that an entitlement was issued, that a consume occurred, and
that a refund or revocation has happened. PPP does not enforce the
counter or deliver the key.

## Four Open Architectural Decisions

The shape of the product depends on four choices that are not made yet.

### 1. Where the counter lives

- on-chain native asset with consume-burn semantics: clean per-event
  proof, ~one tx per consume, payment rail integrated
- keeper-network consensus: cheaper per-consume, requires trusting the
  keeper set on the count
- publisher-tracked: simplest, but the publisher learns every consume

### 2. Key custody and delegation

- author-as-keeper: author runs a small service, must be online, learns
  reader identity at delivery time
- threshold proxy re-encryption (NuCypher, Lit, similar): preserves
  reader privacy from the author, adds external dependency
- witness encryption from payment proof: theoretically attractive,
  currently impractical

### 3. Transferability

- non-transferable entitlement: simpler, no secondary market
- transferable (NFT-shaped): readers can resell unused credits, creates
  a secondary market that competes with primary issuance
- the choice has product and tax implications, not just protocol

### 4. Refund and revocation semantics

- author-initiated revocation: needs delegation revocation if PRE was
  used
- reader-initiated refund window: who arbitrates, and on what timeline
- partial vs full refund: partially-consumed bundles are messy

## Where Zero Knowledge Fits

ZK proofs are useful at two specific spots, neither of which is required
for a v1:

- `reader anonymity at consumption`: prove "I hold a valid entitlement
  issued by author E" without revealing which entitlement.
  Set-membership over the author's entitlement tree. Cardano + Groth16
  set-membership is a workable path.
- `non-revocation under privacy`: prove "my entitlement is not in the
  refunded set" without revealing which entitlement. Same machinery as
  status-list non-revocation in
  [Credential Status](../protocols/credential-status.md).

A simple version can ship without ZK. The author then sees consume
events but not necessarily reader identity, depending on the key custody
choice.

## What Is Parked

- `pre-decision query privacy`: querying truth-axis facts for CID X
  reveals interest in CID X. Solutions (PIR, batched fetch, trusted
  aggregator) all have costs. Defer.
- `keeper-reader collusion`: a keeper that colludes with a reader can
  re-issue delegations for free. Threshold schemes mitigate but do not
  eliminate.
- `once-decrypted-republished`: encryption gates first access, not
  redistribution. The product sells friction, not impossibility. This
  is the same constraint as every DRM scheme.

## Honest Read

This is not a refinement of the journalism integrity story. It is an
adjacent product loop that *consumes* the integrity layer to inform a
paid-access decision. The two axes compose at the reader app, not at the
protocol level.

The four architectural decisions above should be settled before any
blueprint is drafted. Until then, this note exists to keep the design
space visible.

## See Also

- [Shared Subject](shared-subject.md)
- [Independent Journalism candidate](../candidates/independent-journalism/index.md)
- [Endorsement With Revocation blueprint](../protocols/endorsement-with-revocation.md)
- [Credential Status blueprint](../protocols/credential-status.md)
