# Credential Status Blueprint

This blueprint starts from credential status and revocation scenarios such as
W3C Bitstring Status Lists, OpenID4VC, and Hyperledger AnonCreds.

## Scenario

A verifier receives a credential presentation and needs to know whether the
credential is currently valid for a context.

The hard part is often negative:

```text
credential C is not revoked, not suspended, and not superseded for purpose P
```

The issuer, holder, verifier, status publisher, and optional authority each own
different facts.

## Value Proposition

Credential flows already have signatures. PPP is useful when the verifier needs
current peer state and selective disclosure:

- issuer proves issuance without exposing the full issuance set
- status publisher proves non-revocation or suspension status
- holder discloses only the needed credential facts
- verifier can audit the status proof against a committed root
- certifications can summarize policy-specific acceptance

## Shared Claim

One useful first claim is:

```text
credential C is valid for verifier V in context K under policy P
```

That claim is policy-scoped. It is not the same as saying the credential is
universally valid forever.

## Peer-Owned Facts

Examples:

```text
issuer: issued(credential C, subject S, schema X)
issuer: status_list_root(list L, epoch E)
status publisher: not_revoked(credential C, list L, epoch E)
status publisher: not_suspended(credential C, list L, epoch E)
holder: disclosed_attribute(attribute A, predicate Q)
verifier: accepted_presentation(C, context K, policy P)
authority: issuer_authorized(issuer I, schema X)
```

Blocking facts:

```text
issuer excludes revoked(credential C)
issuer excludes suspended(credential C)
issuer excludes superseded_by(credential C, credential C2)
authority excludes issuer_deauthorized(issuer I)
verifier excludes policy_exception(C, context K)
```

## Proof Request

The requester usually knows a credential id, status list index, credential
digest, issuer id, presentation id, or previous acceptance certification.

The request should ask:

```text
known claim: credential C remains acceptable for context K under policy P
include: issuance, status-list commitment, required disclosed predicates
exclude: revoked, suspended, superseded, issuer deauthorized
freshness: status root current within policy window W
disclosure: holder-minimal attributes and status proofs
```

The proof request should avoid broad discovery of a holder's wallet or an
issuer's issuance set.

## Proof Bundle

A credential status proof bundle should carry:

- credential or redacted credential reference
- issuer inclusion proof for issuance if required
- status inclusion or exclusion proof
- status-list root or anchored claim-history reference
- disclosed holder predicates
- policy id and version
- verifier context
- optional acceptance certification

## Certification Fact

A verifier or auditor can publish:

```text
credential_accepted(C, context K, policy P, input_bundle_digest B)
```

The certification must pin:

- credential digest or privacy-preserving credential reference
- context and policy
- issuer and status root references
- disclosed predicate commitments
- input bundle digest
- certifier identity
- certification epoch or ledger position

This lets a downstream process reuse the acceptance fact while preserving an
audit-through path to the original presentation and status proofs.

## Infrastructure Path

Start off-chain:

- issuer and status publisher produce signed roots
- holder carries credential and disclosure proofs
- verifier requests status proofs when needed

Use on-chain anchors when status timing or public non-equivocation matters:

- issuer anchors namespace `credential-status`
- scope can be issuer, schema, or status list
- verifier checks the latest status root before accepting a claim

Privacy is a primary design constraint. Anchoring roots should not reveal which
credential is being checked.

## Verifier Outcomes

The verifier should return:

```text
pass
fail
revoked
suspended
superseded
incomplete
stale_status
unsupported_exclusion
issuer_unauthorized
policy_mismatch
```

`pass` requires the required negative evidence. Absence of a disclosed
revocation fact is not proof of non-revocation.

## Source Anchors

- [W3C VC Status Lists candidate](../candidates/w3c-vc-status-lists/index.md)
- [OpenID4VC candidate](../candidates/openid4vc/index.md)
- [Hyperledger AnonCreds candidate](../candidates/hyperledger-anoncreds/index.md)
- [Credential Lifecycle sketch](../candidates/credential-lifecycle/index.md)
- [Bitstring Status List v1.0](https://www.w3.org/TR/vc-bitstring-status-list/)

