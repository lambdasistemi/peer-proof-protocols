# Credential Lifecycle

## Peers

- issuer
- holder
- verifier
- revoker or authority

## Base Facts

- issuance
- presentation or consent
- validation result
- revocation

## Shared Claim

- `credential C is valid for use in context K`

## Why It Is Strong

- exclusion is central, especially "not revoked"
- selective disclosure is usually required
- certifications can become reusable facts
- lifecycle state changes over time

## Certification Ladder

A verifier can publish:

- `credential C accepted for context K under policy P`

while still allowing downstream audit through to the issuance and revocation
proofs.
