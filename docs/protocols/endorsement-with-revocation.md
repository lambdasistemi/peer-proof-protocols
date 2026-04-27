# Endorsement With Revocation Blueprint

This blueprint starts from settings where one peer endorses another peer's
content-addressed claim, and where withdrawing that endorsement is as
important as publishing it. The motivating examples are independent
journalism, security advisories, content authenticity, and standards-body
recognition.

## Scenario

A reader, court, platform, or downstream system needs to know whether a
given piece of content is currently endorsed by a set of relevant peers,
or whether one or more of those endorsements has been revoked.

The hard part is often negative:

```text
content X is currently endorsed by E and not revoked by E for context K
```

The author, endorser, archive, and optional authority each own different
facts. A signature on yesterday's endorsement does not prove that the
endorsement still stands today.

## Value Proposition

Plain signatures can say that one endorser signed one approval. PPP adds
value when the verifier needs:

- inclusion of the endorsement in the endorser's current state
- exclusion of any retraction, correction, or withdrawal in that state
- selective disclosure across an endorser's portfolio, so a verifier
  learns about content X without enumerating everything else the endorser
  has touched
- a certification layer that turns a checked endorsement bundle into a
  reusable downstream fact

Endorsement and revocation must be modeled symmetrically. An endorsement
that cannot be cleanly withdrawn is a trap for the endorser and a hazard
for the verifier.

## Shared Claim

One useful first claim is:

```text
content X is currently endorsed by endorser E for purpose P at epoch T
```

A stronger downstream claim is:

```text
content X is currently endorsed by quorum Q of endorsers under policy P
```

Both are scoped. Neither says the content is universally true.

## Peer-Owned Facts

Examples:

```text
author: published(content X, digest D, archive A)
author: succeeded_by(content X, content X')
endorser: endorses(content X, purpose P, epoch T)
endorser: scope(content X, scope S)
fact_checker: review_completed(content X, report R, policy P)
archive: archived(content X, digest D, timestamp T)
authority: recognizes(endorser E, scope S)
```

Blocking facts:

```text
endorser excludes retracted(content X)
endorser excludes superseded(content X, by content X')
endorser excludes endorsement_paused(content X)
fact_checker excludes findings_invalidated(report R)
authority excludes endorser_derecognized(endorser E, scope S)
archive excludes archive_tampered(content X)
```

Revocation is expressed as the appearance of an exclusion-blocking fact in
the endorser's tree, not as the deletion of the original endorsement. The
historical endorsement remains discoverable; what changed is that its
current validity is no longer provable.

## Proof Request

The requester usually knows a content digest, an article URI, an endorser
identity, a fact-check report id, an archive id, or a previous
certification.

The request should ask:

```text
known claim: content X is currently endorsed by E for purpose P
include: endorsement, scope, fact-check report, archive timestamp
exclude: retracted, superseded, endorsement_paused, findings_invalidated,
         endorser_derecognized
freshness: endorser root within policy window W
disclosure: endorser slice limited to content X and its supersession chain
```

The proof request should not invite enumeration of an endorser's full
endorsement portfolio.

## Proof Bundle

An endorsement-with-revocation proof bundle should carry:

- content digest and content addressing reference
- endorsement inclusion proof against the endorser's current root
- exclusion proofs for retraction, supersession, and pause
- fact-check report reference and inclusion proof, when in scope
- archive timestamp proof
- authority recognition proof when the policy requires it
- policy id, purpose, and version
- optional certification fact
- optional audit-through input bundle

## Certification Fact

A certifier or auditor can publish:

```text
endorsement_current(content X, purpose P, policy P', input_bundle_digest B)
```

The certification must pin:

- content digest
- endorser identity and current endorser root
- fact-check report digest and report-publisher root, if applicable
- archive root and timestamp
- policy id and version
- input bundle digest
- certifier identity
- certification epoch or ledger position

This supports two consumption modes:

- `trust certifier`: verify the `endorsement_current` fact and the
  certifier's root, and stop there
- `audit through`: verify the certification fact and re-check every
  referenced peer's inclusion and exclusion proofs

The certifier is itself just another peer. Nothing in the bundle treats
the certification as authoritative beyond what the consumer's policy
admits.

## Infrastructure Path

Start off-chain with signed peer roots and pull-based proof bundles:

- endorsers publish signed roots on their own domains
- archives publish signed timestamps with their own roots
- the verifier composes inclusion and exclusion proofs without a central
  registry

Move roots on-chain when timing, public non-equivocation, or smart-contract
consumption matters:

- each peer anchors namespaces such as `endorsements`, `retractions`,
  `fact-checks`, or `archives`
- a downstream consumer (court, platform, payment rail) can reference the
  current certifier root and verify the submitted bundle on-chain

Identity binding for endorsers is out of scope for the protocol itself.
Outlet-to-key binding lives in DNS, `.well-known` files, or W3C DIDs on
the outlet's own domain. PPP only consumes the binding it is given.

## Verifier Outcomes

The verifier should return:

```text
pass
fail
retracted
superseded
endorsement_paused
findings_invalidated
endorser_derecognized
incomplete
stale_root
unsupported_exclusion
policy_mismatch
unauthorized_peer
```

`pass` requires the negative evidence the policy lists. Absence of a
disclosed retraction is not the same as a proof of non-retraction.

## Source Anchors

- [Independent Journalism candidate](../candidates/independent-journalism/index.md)
- [Credential Status blueprint](credential-status.md)
- [Composition and Certification](../concepts/composition-and-certification.md)
- [Audit Through vs Trust Certifier](../patterns/audit-through-vs-trust-certifier.md)
- [Inclusion, Exclusion, Completeness](../concepts/inclusion-exclusion-completeness.md)
- [C2PA specification](https://c2pa.org/specifications/specifications/2.0/index.html)
- [schema.org ClaimReview vocabulary](https://schema.org/ClaimReview)
