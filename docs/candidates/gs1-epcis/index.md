# GS1 EPCIS

GS1 EPCIS is a visibility standard for sharing event data about products,
assets, and documents across organizations.

## Fit

This is an excellent peer-proof candidate because each participant can author
its own event stream while downstream parties only need proofs for the relevant
events, statuses, and custody claims.

## Likely Software Role

- peer-side EPCIS event publisher or consumer
- verifier for custody, status, and certification claims
- proof-bundle layer over EPCIS event subsets
- certification layer that promotes verified event bundles into new facts

## IP / License Signals

Risk level: `Medium`

- GS1 says it seeks to make standards implementable on a royalty-free basis to
  the greatest extent possible.
- The GS1 IP Policy is implementation-friendly, but it is not a license to
  republish GS1 standards text or use GS1 marks loosely.
- The safest move is to implement the standard, not to redistribute GS1
  materials as if they were ours.

## Main Non-License Risk

- misuse of GS1 identifiers or vocabularies
- overstating conformance or endorsement
- turning ourselves into a de facto industry registry rather than a peer-side
  tool

## Take

This is one of the best non-software candidates in the set. The clean role is a
peer-side EPCIS toolkit and verifier, not a GS1 conformance body.

## Spec Status

- First PPP feature spec:
  [GS1 EPCIS Peer Traceability Proofs](https://github.com/lambdasistemi/peer-proof-protocols/blob/main/specs/002-gs1-epcis-peer-traceability/spec.md)

## Sources

- [EPCIS & CBV | GS1](https://ref.gs1.org/standards/epcis/)
- [GS1 Intellectual Property Policy](https://ref.gs1.org/gs1/ip-policy/)
- [GS1 FAQ on the IP Policy](https://support.gs1.org/support/solutions/articles/43000734458-how-can-i-learn-about-the-gs1-intellectual-property-ip-policy-)
