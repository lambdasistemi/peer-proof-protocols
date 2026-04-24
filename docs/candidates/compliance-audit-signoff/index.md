# Compliance Audit-Signoff

Compliance and audit-signoff is a protocol family rather than a single external
standard. It covers workflows where evidence producers, reviewers, and auditors
promote verified bundles into reusable certification facts.

## Fit

This is a strong Merkle candidate because it has a natural certification ladder:
base evidence, derived control results, reviewer signoff, and downstream audit
reuse.

## Likely Software Role

- evidence workflow toolkit
- verifier and policy engine
- certification emitter
- internal deployment kit for one organization or one audit relationship

## IP / License Signals

Risk level: `Low`

- There is no single external protocol owner we must license from if we define
  our own protocol or build on open components.
- The real issue is not protocol copyright; it is how we describe what the
  software proves.

## Main Non-License Risk

- regulated-sector obligations
- overclaiming compliance outcomes
- retention, privacy, and evidentiary handling requirements

## Take

This is one of the cleanest certification-fact spaces. It is attractive if we
stay disciplined about being software and workflow infrastructure rather than an
official compliance authority.

## Sources

- [Composition and Certification](../../concepts/composition-and-certification.md)
- [Certification Facts](../../patterns/certification-facts.md)
- [Fit Rubric](../../concepts/fit-rubric.md)
