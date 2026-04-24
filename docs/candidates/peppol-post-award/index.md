# Peppol Post-Award

Peppol post-award covers document exchange after sourcing, such as orders,
despatch advice, invoices, and related validations across the Peppol Network.

## Fit

This is a strong Merkle candidate because buyers, suppliers, and service
providers each own different facts, and later disputes often hinge on exact
document state, receipt, and non-cancellation claims.

## Likely Software Role

- buyer-side or supplier-side middleware
- validator and proof exporter for business document bundles
- private verifier that checks claims without trying to run the public network
- internal workflow tooling around Peppol messages

## IP / License Signals

Risk level: `Medium-High`

- OpenPeppol's BIS documents are copyrighted.
- OpenPeppol says BIS documents may not be modified, redistributed, sold, or
  repackaged without prior consent.
- Implementing against the specifications is one thing; republishing them or
  presenting ourselves as a Peppol service provider is another.

## Main Non-License Risk

- access point and service-provider accreditation
- Peppol Authority agreements and jurisdiction-specific requirements
- reliance on network services like SML, SMP, PKI, and certified access points

## Take

This is a strong business wedge if we stay on the participant side. The risky
move is becoming a Peppol-certified operator without entering the full OpenPeppol
governance structure.

## Sources

- [Peppol Interoperability Framework](https://peppol.org/learn-more/peppol-interoperability-framework/)
- [Service Provider Agreement](https://peppol.org/documentation/governance-documentation/service-provider-agreement/)
- [Peppol BIS Billing 3.0](https://docs.peppol.eu/poacc/billing/3.0/2025-Q2/bis/)
