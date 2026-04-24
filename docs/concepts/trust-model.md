# Trust Model

The trust model is intentionally narrow.

## Peers

Peers are authoritative only for their own facts.

They may:

- publish roots
- disclose facts with proofs
- verify bundles from other peers
- publish certification facts in their own trees

They may not:

- rewrite another peer's facts
- claim semantic authority over another peer's tree

## Verifiers

Verifiers should be able to:

- inspect a received proof bundle
- verify only the referenced slices
- evaluate the shared claim from explicit rules

They should not need:

- hidden operator state
- the full global database
- a privileged coordinator to tell them what is true

## Witnesses And Publication Layers

Witnesses, feeds, relays, and settlement layers may help with:

- ordering
- timestamping
- publication
- escrow

They should not:

- own the only meaningful state
- decide semantic truth for peer-owned facts

## Certifiers

Certifiers are still peers.

Their role is special only in that they verify an input bundle and promote the
result into a new certified fact. That does not make them a central oracle.

The protocol must say whether downstream users:

- trust the certifier directly
- audit through the certifier to the inputs
- or support both modes
