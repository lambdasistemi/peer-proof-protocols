# Dispute Resolution

Dispute resolution is another protocol family rather than a single standards
body deliverable. Claimants, respondents, and arbitrators each contribute
different evidence and higher-order claims.

## Fit

This is a strong Merkle candidate because evidence is naturally multi-party,
selective disclosure matters, and later certifications can become decisive facts
in settlement.

## Likely Software Role

- evidence bundle format and verifier
- workflow tooling for claim, response, and certification steps
- arbitrator or reviewer support tooling
- replayable dispute record for later audit

## IP / License Signals

Risk level: `Low`

- There is no obvious protocol-license blocker if we define our own workflow or
  profile open components.
- The protocol-license problem is small compared with the surrounding legal
  domain.

## Main Non-License Risk

- legal-service boundaries
- evidence retention and confidentiality
- escalation from technical workflow tool into regulated adjudication

## Take

This is a very good Merkle fit, but the product framing matters a lot. The safe
position is evidence tooling and verification infrastructure, not pretending to
be a court or regulated arbitration body.

## Sources

- [Composition and Certification](../../concepts/composition-and-certification.md)
- [Audit Through vs Trust Certifier](../../patterns/audit-through-vs-trust-certifier.md)
- [Fit Rubric](../../concepts/fit-rubric.md)
