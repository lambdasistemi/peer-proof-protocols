# Peppol Pre-Award

Peppol pre-award covers tendering-side document exchange, including tender
submission, qualification documents, packaging, signing, and encryption.

## Fit

This is a strong Merkle candidate because contracting authorities, economic
operators, and service providers each own different state transitions, and
negative claims such as "not tampered with" or "not withdrawn" matter.

## Likely Software Role

- tendering-system side packager and verifier
- proof exporter for tender submission and qualification bundles
- private workflow tool for one procurement participant
- conformance helper, not public network operator

## IP / License Signals

Risk level: `Medium-High`

- Pre-award BIS materials are copyrighted and may not be modified or repackaged
  without consent.
- The pre-award cryptographic specifications explicitly target tendering
  systems, access points, and software developers, so implementation is expected
  even though redistribution of the docs is constrained.

## Main Non-License Risk

- procurement-law requirements
- service-provider accreditation if we move into network roles
- handling encrypted tender content and retention safely

## Take

This is a good fit if we remain a participant-side or private-platform tool.
The moment we want to be an official Peppol service provider, agreements and
governance become central.

## Sources

- [Pre-Award BIS cryptographic specifications](https://docs.peppol.eu/pracc/files/BIS-eDocuments-guide-for-pre-award-v1.3.pdf)
- [Peppol Interoperability Framework](https://peppol.org/learn-more/peppol-interoperability-framework/)
