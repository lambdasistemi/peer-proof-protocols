# Anchor Identity And Claim History

PPP uses the blockchain to anchor claim histories, not individual facts.

The design target is:

```text
one owner key + one namespace + one scope -> one canonical claim-history anchor
```

Each anchor is an append-only history of roots for a peer-controlled claim set.
The facts and proofs stay off-chain. The chain stores the small commitment that
lets others find the current accepted root history and check that it evolved
according to the rules.

## Anchor Identity

An anchor identity should include:

- network
- validator version or script reference
- owner public key or stable owner identifier
- namespace
- scope digest
- anchor token policy id
- anchor token name

The owner public key separates who controls the anchor. The namespace and scope
separate what the anchor is about.

Examples:

```text
owner Alice, namespace provenance, scope package P
owner Alice, namespace credentials, scope issuer I
owner Bob, namespace provenance, scope package P
```

These are different anchor identities.

## Why The Token Is Needed

If the validator is parameterized by the owner public key, anyone can compute
the owner-specific script address:

```text
script_address = hash(anchor_validator(owner_public_key))
```

That helps discovery, but it does not identify the canonical UTxO by itself.
Anyone can send arbitrary UTxOs to a script address. Locking value at an address
does not run the validator.

So the protocol needs a unique state token:

```text
canonical anchor UTxO = the UTxO holding anchor_token
```

Junk UTxOs at the same script address are ignored because they do not hold the
anchor token.

The practical invariant is:

```text
one minted anchor token -> one current UTxO holding it -> one canonical anchor state
```

## Minting The Anchor Token

For each anchor identity, the initializer mints one anchor NFT or state token.

A one-shot minting policy should require:

```text
consume seed UTxO S
mint exactly +1 of token T under this policy
mint no other token under this policy
derive T from owner, namespace, scope, and S
```

Because the seed UTxO can be consumed only once, the same minting policy cannot
mint the same anchor token again.

The minting policy gives uniqueness. The anchor validator gives valid
evolution.

## Anchor Datum

The anchor UTxO datum should describe the current state of one claim-history
anchor:

```text
owner_public_key
namespace
scope_digest
anchor_token
sequence_number
latest_claim_root
claim_history_root
previous_event_id
last_publication_slot
schema_version
```

The `latest_claim_root` is a convenience pointer to the newest committed claim
set. The `claim_history_root` commits to the append-only history of claim roots
or claim events.

## Claim Events

A claim-history event records one append to the owner's namespace/scope
history.

The event can contain:

```text
sequence_number
event_type
claim_root
previous_claim_root
previous_event_id
publication_slot
claim_scope_digest
payload_digest
```

The payload itself stays off-chain. The payload may include facts, root
semantics, certification facts, revocation facts, or policy references.

The important point is that the event is committed into the claim-history tree,
and the on-chain validator only accepts the next datum if it is a valid append.

## Update Rule

To update the anchor, the transaction spends the current UTxO holding the
anchor token and creates the next UTxO holding the same token.

The validator checks:

```text
old output holds anchor token T
new output preserves anchor token T
new output is locked by the same owner-parameterized validator
owner authorizes the update
namespace and scope are preserved
sequence_number increases by 1
new claim_history_root is old history plus one valid append
latest_claim_root matches the appended event
publication slot satisfies the protocol bounds
```

This enforces:

```text
one live state per anchor token
append-only claim history
monotonic sequence
owner-controlled updates
```

## Discoverability

The owner distributes the stable anchor identity, not the current UTxO.

A proof bundle should carry:

```text
anchor identity
facts
inclusion proofs
exclusion proofs
claim-root proof
claim-history proof
optional current out_ref as a cache hint
```

The verifier resolves the anchor:

```text
derive or read anchor token
query chain or indexer for UTxO holding that token
verify UTxO is at the expected owner-parameterized script
verify datum owner, namespace, and scope match the anchor identity
use the UTxO as a reference input
```

The current `out_ref` may be included in the bundle for convenience, but it is
only a hint. The token is the stable locator.

## Proof Distribution Is Pull-Oriented

The anchor is public. The proofs are not automatically public.

Redistributing every proof proactively is the wrong default because proofs are:

- claim-specific
- transition-specific
- sometimes privacy-sensitive
- potentially large compared with the anchor
- stale when roots or policies move

New claim publication is different. If an author wants others to learn a new
claim, endorsement, score, or certification, distributing that claim artifact is
the author's push responsibility.

Proof distribution should be pull-oriented only after another party already
knows the claim and needs current evidence that the author still stands behind
it.

Instead, the default UX should be pull-oriented:

```text
author pushes or publishes claim C
verifier or consumer later needs current proof for known claim C
verifier sends proof request for C to the holder
holder returns the minimal proof bundle or current status for C
verifier resolves the anchor on-chain
verifier checks the bundle against the current anchor state
```

The holder may be the producer, claimant, certifier, wallet, agent, or any party
that already possesses the facts and can compute or cache the relevant proofs.
Once a proof bundle is disclosed, it is portable and can be relayed by others,
but the protocol should not require the producer to push every possible proof to
every possible verifier.

A proof request should specify:

```text
anchor identity
known claim references
requested claim status or transition
required positive facts
required negative facts
freshness bound
policy id and version
desired disclosure limits
```

The request format should be explicit enough to become a transport-neutral
language. See [Proof Request Language](proof-request-language.md).

The response should include:

```text
disclosed facts
inclusion proofs
exclusion proofs
claim-root proof
claim-history proof
policy metadata
optional current out_ref hint
```

If the holder cannot prove a required exclusion or freshness condition, the
response should say so explicitly. The verifier then returns `incomplete` or
`unsupported`, not `pass`.

Requests that ask a peer to create a new claim, fill a partial claim, score a
subject, or endorse someone else's claim are claim-authoring requests rather
than proof requests. They create new state first; proof requests can verify that
state later.

## Smart Contract Consumption

A consuming protocol validator can use the anchor UTxO as a reference input.

The redeemer or transaction data carries:

```text
disclosed facts
proofs against claim root
proof that claim root is in claim history
transition evidence contract
```

The validator checks:

```text
reference input holds expected anchor token
reference datum matches owner, namespace, and scope
claim root is included in current claim_history_root
positive facts are included in claim root
negative blocking facts are excluded from claim root
transition rule passes
```

This lets a consumer contract verify that a producer did not cheat about its
current claim history, without putting the producer's facts on-chain.

## Relationship To KERI

This is close to KERI culture.

The rough mapping is:

```text
owner identifier -> AID-like peer identity
claim-history events -> interaction events for fact roots
key update events -> establishment events if rotation is enabled
anchor token -> unique on-chain locator for one peer log
chain confirmation -> witness/publication receipt
```

PPP should reuse that mental model where it helps, while keeping the payload
focused on application claim roots and proof-carrying state transitions.
