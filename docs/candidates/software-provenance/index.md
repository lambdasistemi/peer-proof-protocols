# Software Provenance

## Peers

- author or maintainer
- builder or CI
- reviewer
- deployer

## Base Facts

- source intent
- build attestation
- review approval
- deployment fact

## Shared Claim

- `artifact Y is the reviewed and deployed result of source X`

## Why It Is Strong

- no single peer owns the full provenance story
- selective disclosure matters
- historical roots matter
- certification facts are natural, such as `release approved` or
  `deployment compliant`

## Certification Ladder

An auditor can certify:

- `artifact Y satisfies provenance policy P`

from a verified bundle of source, build, review, and deployment proofs.
