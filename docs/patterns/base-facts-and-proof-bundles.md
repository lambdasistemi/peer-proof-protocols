# Base Facts and Proof Bundles

The smallest useful building block is a fact bundle with proofs.

## Base Facts

A base fact is a peer-authored claim such as:

- `delivered(milestone M, artifact A)`
- `accepted(order O)`
- `revoked(credential C)`

It is authored by one peer and committed into that peer's tree.

## Proof Bundle

A proof bundle typically carries:

- canonical facts
- inclusion or exclusion proofs
- referenced peer roots or checkpoints
- peer identities or key references
- enough metadata to interpret the facts

## Design Goal

A verifier should be able to:

- recompute the relevant fact commitments
- verify the proofs against trusted roots
- evaluate the protocol rule

without needing the full peer log.

## Common Mistake

Do not confuse:

- the fact bundle
- the proof bundle
- the derived or certified claim

They are different layers and should be modeled separately.
