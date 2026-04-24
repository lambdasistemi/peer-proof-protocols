# SWIFT Documentary Credits

SWIFT documentary credits sit in the trade-finance messaging world, where banks
exchange standardized messages around letters of credit and related events.

## Fit

This is a rich multi-party protocol: issuing bank, advising bank, confirming
bank, beneficiary, and applicant all contribute facts that can be chained and
certified.

## Likely Software Role

- internal bank or corporate workflow tooling
- verifier for message bundles and approval chains
- evidence exporter for audit or dispute support
- simulator or training environment inside licensed contexts

## IP / License Signals

Risk level: `High`

- Swift Standards and related documentation are governed by a Swift Standards
  IPR end-user license agreement.
- This is the clearest case in the set where contractual and IP boundaries are
  front and center.
- The safe move is to build internal tooling for licensed users, not to
  repackage standards content or present ourselves as a Swift network service.

## Main Non-License Risk

- banking regulation
- operational liability
- network participation requirements and customer expectations

## Take

This is a compelling protocol shape, but it is the highest-friction candidate
for an external product. It is better as research or internal tooling than as an
early public-facing target.

## Sources

- [Swift Standards IPR Policy - End-User License Agreement](https://www.swift.com/about-us/legal/ipr-policies/swift-standards-ipr-policy-end-user-license-agreement)
- [Swift Standards overview](https://www.swift.com/swift-standards-0)
- [Developer resource centre: Standards](https://www.swift.com/ordering-support/developer-resource-centre/develop/standards)
