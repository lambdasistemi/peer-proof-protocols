# in-toto

in-toto is a framework for generating and verifying attestations about how
software was produced.

## Fit

This is an excellent Merkle candidate because it already thinks in terms of
step-level attestations, authorized functionaries, and higher-order verification
rules. It maps naturally onto certification facts.

## Likely Software Role

- attestation producer
- verifier and policy engine
- certification layer that promotes verified bundles into reusable facts
- bridge between per-peer authenticated state and in-toto attestations

## IP / License Signals

Risk level: `Low`

- The main in-toto attestation repository is under Apache 2.0.
- This is a permissive implementation environment compared with the trade and
  network-governed standards above.

## Main Non-License Risk

- overlap with adjacent provenance stacks such as SLSA and Sigstore
- deciding whether we are extending in-toto, embedding it, or merely borrowing
  the protocol pattern

## Take

This is one of the cleanest places to explore certification facts without
fighting protocol licensing. It is likely the best software-first candidate.

## Spec Status

- Proposed PPP spec and plan:
  [in-toto Peer Provenance Proofs](https://github.com/lambdasistemi/peer-proof-protocols/blob/main/specs/003-in-toto-peer-provenance/spec.md)

## Sources

- [in-toto getting started](https://in-toto.io/docs/getting-started/)
- [in-toto Attestation Framework repository](https://github.com/in-toto/attestation)
- [in-toto Attestation Framework Apache 2.0 license](https://raw.githubusercontent.com/in-toto/attestation/main/LICENSE)
