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

## Daydream — Atomic Key-for-ADA Exchange

This section is daydreaming, not planning. None of it is required for a
v1, or even a v2. It is recorded so the design space stays visible.

The fantasy: a Cardano validator atomically exchanges ADA for a
per-article decryption key, with a cryptographic guarantee that the key
actually decrypts the ciphertext the reader is paying for. No keepers,
no trusted publisher, no off-chain dance.

Three approaches in the literature are worth naming.

### Zero-Knowledge Contingent Payment (ZKCP)

Demonstrated by Maxwell, Poelstra, and Wuille on Bitcoin in 2016 (the
"Sudoku for Bitcoin" demo). Shape:

```text
1. author publishes ciphertext C and a ZK proof:
   "I know K such that decrypt(C, K) has digest D and hash(K) = H"
2. reader posts a Cardano script:
   "pay author iff redeemer R satisfies hash(R) = H"
3. author claims ADA by revealing K in the spending tx
4. reader extracts K from the chain and decrypts C locally
```

The validator never sees `K` until step 3 and only releases ADA when
`K` is revealed. The ZK proof from step 1 is what binds the released key
to the right ciphertext; without it the seller could publish any
preimage and steal the payment.

Costs:

- proof generation at issuance is heavy (Groth16 or Plonk over a
  decryption circuit plus hash openings); minutes per bundle is a
  realistic order of magnitude
- requires SNARK-friendly primitives (Poseidon hash, ChaCha20-style or
  AES-in-SNARK encryption)

### Adaptor Signatures Plus Issuance-Time ZK

Adaptor signatures (Poelstra, "scriptless scripts") use Schnorr-like
algebra to atomically swap a payment for a secret without a SNARK at
consume time. Cardano has Schnorr (secp256k1) and EdDSA primitives in
Plutus, so this is feasible.

The tradeoff: adaptor signatures bind a payment to a secret but do not
prove what that secret decrypts. So this approach pairs:

- one issuance-time ZK proof binding all `K_i` to all `C_i`
- cheap per-consume adaptor-signature swaps

Cleaner separation of concerns than ZKCP-per-consume, and lighter at
the consume step.

### Witness Encryption

The dream primitive: encrypt `K` such that anyone holding a valid
proof of payment can decrypt. Currently impractical at scale.
Mentioned only for completeness.

### Why This Is Daydreaming

- proof generation at issuance is in the minutes range, which pushes
  the model toward "publish a long-lived bundle once, sell many reads"
  — a real product constraint, not a deal-breaker, but not free
- the per-consume on-chain dance is only economic on Hydra L2, not on
  L1; see decision 1 above
- a v0 will use a trusted publisher or threshold keepers, not any of
  this
- but the architecture should not foreclose these options. The four
  decisions above remain compatible with adopting ZKCP or adaptor
  signatures later if the cryptography matures and the product needs
  the trust reduction

External references worth a search rather than a fragile link:

- Maxwell, Poelstra, Wuille — "Zero-Knowledge Contingent Payments"
  (Bitcoin, 2016)
- Poelstra — "Scriptless Scripts" / adaptor signatures (Bitcoin
  scaling notes, 2017–)
- [Hydra Head Protocol](https://hydra.family/head-protocol/) — the
  Cardano L2 path that makes per-consume on-chain dances economic

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
