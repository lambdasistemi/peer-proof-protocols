# Supply Chain Traceability Blueprint

This blueprint starts from real supply-chain event protocols such as GS1 EPCIS
and DCSA Track and Trace.

## Scenario

A downstream buyer, regulator, insurer, marketplace, or customer needs to verify
that an item, shipment, or container followed an acceptable path.

The hard part is not one signed event. The hard part is that several parties
own different slices of the story:

- manufacturer or seller owns production and commissioning events
- carrier owns transport and exception events
- warehouse owns receipt, storage, and release events
- buyer owns acceptance or rejection events
- certifier may own the derived claim that the path satisfies policy

## Value Proposition

The verifier should not need the full EPCIS repository, carrier database, or
platform registry.

PPP adds value when the verifier can receive only the relevant event slice plus
proofs that:

- required events are included in each peer's committed state
- blocking events are excluded from the declared scope
- each event root is fresh enough for the policy
- a certifier's derived traceability result pins the verified inputs

## Shared Claim

One useful first claim is:

```text
item or shipment S is traceable from origin O to destination D under policy P
```

This should be evaluated from peer-authored facts, not from a central
traceability oracle.

## Peer-Owned Facts

Examples:

```text
producer: commissioned(item S)
producer: packed(item S, lot L)
carrier: received_for_transport(shipment S)
carrier: departed(location A)
carrier: arrived(location B)
warehouse: received(shipment S)
warehouse: released(shipment S)
buyer: accepted(shipment S)
certifier: traceability_passed(S, policy P, input_bundle_digest B)
```

Blocking facts are equally important:

```text
producer excludes recalled(item S)
carrier excludes lost(shipment S)
carrier excludes temperature_excursion(shipment S)
warehouse excludes active_hold(shipment S)
buyer excludes rejected(shipment S)
```

## Proof Request

The requester usually knows the shipment, item, EPC, lot, bill of lading,
container id, or previous certification id.

The request should ask for current support of a known traceability claim:

```text
known claim: shipment S traceable under policy P
include: required custody and transformation events
exclude: recall, lost shipment, active hold, rejected receipt
freshness: roots current before settlement or release deadline
disclosure: only events relevant to S
```

It should not ask a carrier or repository to disclose every event it has for a
route, customer, or product family.

## Proof Bundle

A traceability proof bundle should carry:

- canonical event facts for the item or shipment
- inclusion proofs for required events
- exclusion proofs for blocking event keys
- peer root references or anchored claim-history references
- policy id and version
- optional certification fact
- optional audit-through input bundle

## Certification Fact

A certifier can publish:

```text
traceability_certified(S, policy P, input_bundle_digest B, result pass)
```

The certification must pin:

- subject item, lot, shipment, or container
- policy id and version
- input event bundle digest
- input peer roots or anchored checkpoints
- certifier identity
- certification epoch or ledger position

Downstream users can either trust the certifier's root or audit through to the
underlying event proofs.

## Infrastructure Path

Start off-chain:

- each peer signs roots for its own event state
- holders respond to proof requests with scoped proof bundles
- verifiers check bundles locally

Move to on-chain anchors when settlement, regulatory deadlines, or disputes need
public ordering:

- each peer anchors a namespace such as `traceability`
- scope can be an item class, lot, shipment, or participant-specific event set
- smart contracts can consume a traceability certification as a reference input
  plus proof bundle

## Verifier Outcomes

The verifier should return:

```text
pass
fail
incomplete
unsupported_exclusion
stale_root
policy_mismatch
unauthorized_peer
```

`incomplete` is the correct result when only positive event evidence is
provided and required blocking-event exclusions are missing.

## Source Anchors

- [GS1 EPCIS candidate](../candidates/gs1-epcis/index.md)
- [DCSA Track and Trace candidate](../candidates/dcsa-track-and-trace/index.md)
- [GS1 EPCIS standard reference](https://ref.gs1.org/standards/epcis/)
- [DCSA Track and Trace overview](https://dcsa.org/standards/track-and-trace)

