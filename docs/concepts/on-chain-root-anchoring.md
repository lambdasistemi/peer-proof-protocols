# On-Chain Root Anchoring

Moving peer Merkle roots on-chain changes the publication layer, not the
meaning of peer facts.

The preliminary PPP work is useful because an on-chain root is only valuable if
the off-chain layer already knows:

- what a leaf means
- who authored it
- which peer root commits to it
- how inclusion and exclusion proofs verify
- what policy evaluates the disclosed facts
- what a certification fact pins
- what trust assumptions remain

Without those answers, a blockchain only stores expensive opaque hashes.

## Baseline: Off-Chain Signed Roots

In the off-chain PPP model, each peer signs roots over its own state and sends
proof bundles to verifiers.

This already gives:

- peer-authored facts
- portable inclusion and exclusion proofs
- selective disclosure
- certification facts with pinned inputs
- independent verification of the received bundle

The verifier's trust surface is:

- trust the peer identity and signing key
- trust that the received root is the one the peer meant to publish
- trust the freshness statement supplied with the bundle
- trust that missing roots or missing proofs are reported honestly
- trust the policy used to evaluate the disclosed facts

That is enough for many bilateral flows, but it is weak when disputes depend on
publication time, latest-known state, or whether a peer showed different roots
to different counterparties.

### No External Clock, No Reliable Timestamp

A signed root has no credible time by itself.

It proves:

```text
peer P signed root R
```

It does not prove:

```text
root R existed before deadline D
```

The peer can include a timestamp in the signed checkpoint, but that timestamp is
still a peer-authored claim unless it is backed by an external publication
event. In a dispute, this matters because a peer could construct an old-looking
root after the fact and claim it represented its earlier state.

So the honest rule is:

```text
without a public ledger or trusted witness, a root's timestamp is not
independently provable
```

Off-chain signed roots are useful for authorship and proof verification. They
are not enough for strong "this was my state before time T" claims.

## What Changes On-Chain

Anchoring a peer root on-chain turns a private or semi-public checkpoint into a
public ledger event.

The trust layer changes in seven main ways.

### 1. Publication Becomes Public

Without the chain, a verifier can check that a peer signed root `R`, but may
not know whether `R` was ever broadly published.

With the chain, anyone can later show:

- this peer committed root `R`
- in this transaction
- at this ledger position
- under this publication policy

The root becomes independently discoverable.

### 2. Time And Ordering Become Ledger Claims

Off-chain timestamps are peer claims. They can be signed, but the peer still
controls when and where they are disclosed.

On-chain anchoring adds consensus ordering. A verifier can compare:

- root `R1` came before root `R2`
- certification root `C` came after its referenced input roots
- a revocation root was published before or after a disputed bundle

This does not prove perfect wall-clock time. It proves a more precise and
useful claim:

```text
root R existed no later than ledger position L
```

If the protocol maps policy deadlines to ledger slots, blocks, or accepted
publication windows, that ledger position becomes the external clock the
verifier can use.

On Cardano, transactions can carry metadata, and confirmed metadata is stored
on-chain. Cardano transactions also have deterministic validation and validity
intervals, so a root publication transaction can be tied to a precise ledger
event and validity window.

### 3. Equivocation Gets Harder To Hide

Off-chain, a peer can sign two different roots for the same epoch and show them
to different counterparties. That can be detected only if the two roots meet in
some dispute or audit.

On-chain, the publication rule can be:

```text
for peer P and sequence n, the accepted root is the root anchored on-chain
under P's root policy
```

If a peer publishes conflicting roots, the conflict is visible. If the on-chain
policy enforces one update path per peer namespace, conflicting publication can
be rejected or made subject to whatever off-chain or on-chain penalty the
protocol defines.

### 4. Freshness Becomes Checkable

With off-chain bundles, freshness is usually a statement inside the bundle:

```text
this proof was generated from peer root R at time T
```

With on-chain roots, freshness can be checked against the latest accepted root
for that peer and scope:

```text
this proof verifies against root R
R was the latest root before policy deadline D
no later revocation root is relevant in scope S
```

The verifier still needs the proof bundle, but no longer depends only on the
claimer to tell them which root was current.

### 5. Backdating Becomes Expensive Or Impossible

The main timestamp value is not that a blockchain is a perfect clock. It is
that a peer cannot cheaply publish a root after an event and later pretend the
root was public before the event.

The verifier can ask:

```text
where was root R externally published before deadline D?
```

If the answer is "nowhere", then the root may still be signed, but it is weak
evidence for a past state claim.

### 6. Certifications Become Reusable Public Milestones

PPP composition says that a verified bundle can become a new certification
fact.

If the certifier's root is anchored on-chain, downstream users can cite:

- the certifier root
- the input peer roots
- the policy identifier
- the certification epoch
- the chain transaction that published the commitment

This makes certified results easier to reuse across customers, audits,
settlement, and delayed disputes.

### 7. Current Claim Histories Become Discoverable

For each owner public key, namespace, and scope, the peer can publish one
canonical claim-history anchor. The current anchor UTxO is discovered by the
unique state token that identifies that history.

The chain does not store every claim. It stores the current commitment to the
owner's append-only history of claim roots.

See [Anchor Identity And Claim History](anchor-identity-and-claim-history.md).

## What Does Not Change

On-chain anchoring does not make facts true.

It does not prove:

- a shipment physically moved
- a scanner found every vulnerability
- a builder was not compromised
- a peer did not lie in its own fact
- an omitted fact exists somewhere else
- a policy is the right policy

It proves a narrower statement:

```text
peer P committed root R to the public ledger under policy A at ledger position L
```

The proof bundle still proves:

```text
fact F is included in, or excluded from, root R under the declared tree semantics
```

The policy still decides:

```text
these verified facts are enough, stale, missing, contradicted, or unsupported
```

## Why This Is The Right Preliminary Step

The current PPP work defines the semantics that an on-chain root will later
amplify.

The order matters:

1. Define peer facts and leaf canonicalization.
2. Define root semantics and proof bundles.
3. Define verifier outcomes and degraded modes.
4. Define certification facts and audit-through behavior.
5. Only then anchor roots on-chain.

If we anchor before that, the chain can only say "some bytes were published".
If we anchor after that, the chain says "this peer publicly committed to this
well-specified state interface".

That is the scaling argument. The expensive, shared layer should only carry the
small commitment that many verifiers can reuse. The heavy evidence remains
off-chain and travels as proof bundles.

## Verifier Flow After Anchoring

With on-chain roots, a verifier receives almost the same proof bundle as before,
but the root checkpoint contains a ledger reference.

The verifier checks:

1. the bundle facts canonicalize to the claimed leaves
2. the Merkle proofs verify against peer root `R`
3. `R` appears in the referenced chain transaction or root-state UTxO
4. the transaction satisfies the root publication policy
5. the root is fresh enough for the claim policy
6. no later relevant revocation or supersession root defeats the claim
7. the cross-peer rule evaluates over the verified facts

This is the useful split:

- the chain verifies publication and lifecycle rules for roots
- the proof bundle verifies selected facts against those roots
- the policy verifies whether the selected facts are enough

The verifier does not rebuild the peer database and does not trust a registry
to interpret the facts.

## Opaque Roots And Protocol Transitions

Merkle roots are intentionally opaque. A verifier cannot look at a root and see
which facts are inside it.

That means a protocol transition cannot be justified by positive facts alone.
The transition must define every fact that must be included and every blocking
fact that must be excluded.

The basic rule is:

```text
not disclosed != absent
```

For a transition to be accepted, the proof bundle must carry the evidence the
transition contract requires.

```text
Transition: state A -> state B
Required positive facts: inclusion proofs for facts that must exist
Required negative facts: exclusion proofs for facts that must not exist
Accepted roots: which peer roots and epochs can support the transition
Freshness bound: how recent the roots must be
Exclusion scope: which committed key space absence is checked against
Unsupported exclusion result: usually incomplete or unsupported, not pass
```

For example, a software release transition might require:

```text
include build_succeeded(artifact D)
include tests_passed(artifact D)
include scan_completed(artifact D)
include publisher_approved(artifact D)
exclude revoked(artifact D)
exclude active_release_hold(artifact D)
exclude failed_required_test(artifact D)
exclude critical_vulnerability_above_threshold(artifact D)
```

If the verifier only receives the positive facts, the release transition is not
proven. It is merely a selective story. The negative facts must either be
excluded against a committed peer key space or the transition must be reported
as incomplete or unsupported.

This is why PPP specs need transition evidence contracts. They turn an opaque
root into a usable state-machine boundary.

## Why This Scales

Putting all facts on-chain does not scale and usually destroys privacy.

Putting only roots on-chain scales better because one root can authenticate many
facts:

- one supplier root can cover many shipment facts
- one builder root can cover many build attestations
- one scanner root can cover many scan results and revocations
- one certifier root can cover many certification facts

Each verifier downloads only the facts and proofs relevant to one claim. The
shared ledger carries the small, reusable commitment that many verifiers can
agree on.

That means the chain is used for the thing it is good at:

- public ordering
- durable timestamping
- non-equivocation pressure
- shared settlement over commitments

The chain is not used as a database for every event.

## Cardano-Specific Shape

There are at least two Cardano publication modes.

### Metadata Publication

A peer can publish a transaction with metadata containing:

- peer identifier
- root value
- root semantics
- sequence or epoch
- scope digest
- previous root reference
- signature or key reference

This is simple and cheap. It gives public timestamping and durable discovery,
but the ledger does not enforce much about root evolution unless additional
scripts or policies are used.

### Scripted Claim-History State

A peer namespace and scope can be represented by a UTxO carrying the current
claim-history state in a datum. Spending that UTxO publishes the next claim
root or claim-history event.

The canonical UTxO should be identified by a unique anchor token:

```text
owner public key + namespace + scope -> anchor token -> current anchor UTxO
```

The token is minted once for the anchor identity and preserved across every
valid update. Anyone can send junk UTxOs to the same script address, but they
cannot forge the anchor token.

The validator can require:

- the authorized peer key or policy witness
- preservation of the anchor token
- a monotonic sequence number
- a valid append to the claim-history root
- preserved owner, namespace, and scope
- validity interval constraints
- optional revocation or key-rotation rules

This is stronger than metadata-only publication because the chain enforces part
of the root lifecycle. It still does not validate hidden leaves or facts; it
validates the publication rules for roots.

For the full discovery model, see
[Anchor Identity And Claim History](anchor-identity-and-claim-history.md).

## Trust-Layer Ladder

PPP can grow through layers:

| Layer | What It Adds | What It Still Does Not Do |
|-------|--------------|---------------------------|
| Signed messages | Authorship of one statement | Set membership, absence, current state |
| Off-chain signed roots | Peer-owned state and proof bundles | Public ordering and global discovery |
| Public witness feeds | Better availability and timestamping | Consensus finality or enforced updates |
| On-chain metadata roots | Public durable ordering of roots | Enforced lifecycle rules |
| Scripted claim-history anchors | Enforced append-only root history | Truth of hidden off-chain facts |
| Certification roots on-chain | Reusable public composed results | Automatic trust in the certifier |

The chain should be treated as a narrow settlement and publication layer. It is
not the peer, not the verifier, and not the oracle.

## Design Consequences

For PPP specs, on-chain readiness means every protocol should eventually define:

- the peer namespace committed on-chain
- the claim scope committed on-chain
- the anchor identity distributed to verifiers
- the anchor token policy and token name
- the claim-history event format
- the root publication payload
- whether roots are metadata-only or script-governed
- root sequence and supersession rules
- key-rotation rules
- how revocation roots affect old bundles
- how verifiers find the latest relevant root
- how proof bundles cite on-chain root transactions
- what remains off-chain and must travel with the bundle

The immediate spec work is therefore not disposable. It is the interface that
lets on-chain roots become meaningful shared commitments instead of raw hashes.

## Sources

- [Cardano transaction metadata](https://developers.cardano.org/docs/transaction-metadata/)
- [Cardano EUTXO model](https://developers.cardano.org/docs/get-started/technical-concepts/eutxo/)
- [Cardano transactions](https://developers.cardano.org/docs/learn/core-concepts/transactions/)
