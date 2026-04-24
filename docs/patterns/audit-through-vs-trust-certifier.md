# Audit Through vs Trust Certifier

Certified derived facts introduce a choice for downstream users.

## Trust Certifier

The verifier checks:

- the certification fact
- the certifier's root

This is cheaper and simpler, but it increases trust in the certifier.

## Audit Through

The verifier checks:

- the certification fact
- the certifier's root
- the referenced input bundle
- the underlying proofs

This is heavier, but it preserves more independent assurance.

## Mixed

Some protocols can support both:

- fast-path trust in the certifier
- slow-path audit through to base proofs

If both are supported, the documentation should say clearly which assurance
level each path gives.
