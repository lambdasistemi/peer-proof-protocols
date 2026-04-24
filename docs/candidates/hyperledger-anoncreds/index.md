# Hyperledger AnonCreds

Hyperledger AnonCreds is a privacy-preserving credential system with selective
disclosure and strong non-revocation semantics.

## Fit

This is an excellent Merkle candidate for privacy-first identity workflows
because exclusion and revocation status matter, while holders should disclose as
little as possible.

## Likely Software Role

- issuer, holder, or verifier toolkit
- revocation or status publisher
- policy layer for reusable certification facts
- bridge between privacy-preserving credentials and peer-authored state

## IP / License Signals

Risk level: `Low`

- Hyperledger AnonCreds code is governed through an Apache 2.0 project charter.
- The charter also states that project documentation is made available under
  CC-BY-4.0.

## Main Non-License Risk

- cryptographic complexity
- wallet interoperability
- operational complexity around revocation and holder UX

## Take

This is one of the best privacy-oriented candidates in the set. If we care about
selective disclosure plus negative claims, it deserves serious attention.

## Sources

- [Hyperledger AnonCreds specification](https://hyperledger.github.io/anoncreds-spec/)
- [AnonCreds Technical Charter](https://hyperledger.github.io/governance/project-charters/AnonCreds%20Technical%20Charter%2010-31-2022.pdf)
