# DCSA Track and Trace

DCSA Track and Trace standardizes cross-carrier shipment tracking events and API
exchange for container shipping.

## Fit

This is a strong Merkle candidate because carriers, shippers, forwarders, and
other logistics actors each own different event claims, while verifiers care
about only a slice of a larger shipment history.

## Likely Software Role

- carrier-side or shipper-side standards adapter
- verifier for shipment event bundles
- proof exporter for selected milestones, custody steps, or exceptions
- certification layer for downstream operational claims

## IP / License Signals

Risk level: `Medium`

- DCSA publishes documentation, versioning guidance, OpenAPI definitions, and
  conformance material openly.
- The main IP concern is not whether we can implement it, but whether we imply
  DCSA endorsement or market ourselves as compliant without doing the real work.

## Main Non-License Risk

- conformance expectations from customers
- pressure to behave like a neutral source of truth for cross-carrier events
- data-quality disputes between participants

## Take

This is a strong candidate if we stay in the role of participant-side tooling or
verifier. It becomes much riskier if we try to become an authoritative network
service.

## Sources

- [Track & Trace standard documentation | DCSA](https://dcsa.org/standards/track-and-trace/standard-documentation-track-and-trace)
- [Track & Trace overview | DCSA](https://dcsa.org/standards/track-and-trace)
- [DCSA standard conformance](https://dcsa.org/standard-conformance)
