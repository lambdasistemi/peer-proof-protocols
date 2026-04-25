# Software Release Provenance Blueprint

This blueprint starts from software provenance protocols such as in-toto and
the existing software-provenance candidate sketch.

## Scenario

A user, deployer, package registry, auditor, or customer needs to verify that a
software artifact was built, reviewed, scanned, and released according to a
policy.

The real-world problem is multi-peer:

- maintainer owns source intent and release approval
- reviewer owns code review or approval facts
- builder owns build and artifact facts
- scanner owns vulnerability or policy scan facts
- deployer owns deployment facts
- release certifier may own the derived release-ready claim

## Value Proposition

Plain signatures can say that one actor signed one attestation. PPP adds value
when a verifier needs a scoped, current, and auditable bundle across several
authorities.

The verifier should be able to check:

- required build, review, and scan facts are included
- blocking facts such as revocation or active hold are excluded
- the roots are fresh enough for release or deployment
- a certification fact pins the input bundle and release policy

## Shared Claim

One useful first claim is:

```text
artifact A is releasable under policy P
```

Another downstream claim is:

```text
deployment D used artifact A that satisfied provenance policy P
```

## Peer-Owned Facts

Examples:

```text
maintainer: source_intent(commit C, artifact A)
reviewer: approved(change C, policy P)
builder: built(commit C, artifact A, environment E)
builder: reproduced_digest(artifact A, digest D)
scanner: scan_completed(artifact A, report R)
scanner: no_critical_findings(artifact A, threshold T)
deployer: deployed(artifact A, environment X)
certifier: release_ready(artifact A, policy P, input_bundle_digest B)
```

Blocking facts:

```text
maintainer excludes revoked_release(artifact A)
reviewer excludes approval_withdrawn(change C)
builder excludes build_invalidated(artifact A)
scanner excludes critical_vulnerability_above_threshold(artifact A)
deployer excludes deployment_rollback_required(artifact A)
```

## Proof Request

The requester usually knows an artifact digest, package version, commit, build
id, release id, or previous release certification.

The request should ask:

```text
known claim: artifact A is releasable under policy P
include: source intent, review approval, build attestation, scan result
exclude: revoked release, withdrawn approval, invalidated build, critical finding
freshness: current before release, deployment, or audit deadline
disclosure: artifact-scoped facts only
```

The proof request should not ask a builder, scanner, or registry to expose its
full job history.

## Proof Bundle

A release provenance proof bundle should carry:

- canonical provenance facts
- inclusion proofs for required facts
- exclusion proofs for blocking facts
- policy id and version
- artifact digest and subject identifiers
- peer roots or anchored claim-history references
- optional certification fact
- optional audit-through input bundle

## Certification Fact

A release certifier can publish:

```text
release_ready(artifact A, policy P, input_bundle_digest B, result pass)
```

The certification must pin:

- artifact digest
- source commit or source subject
- policy id and version
- input bundle digest
- input roots or checkpoints
- certifier identity
- certification epoch or ledger position

This supports two modes:

- `trust certifier`: verify the release-ready fact and certifier root
- `audit through`: verify the release-ready fact and all referenced inputs

## Infrastructure Path

Start with signed peer roots and browser-verifiable proof bundles:

- the verifier can run in CI, CLI, package manager, browser, or deployment gate
- the proof bundle travels with the artifact, release metadata, or deployment
  request

Move roots on-chain when release timing, non-equivocation, or smart-contract
consumption matters:

- each peer anchors namespaces such as `software-provenance`, `builds`, or
  `security-scans`
- a deployment or escrow contract can reference the current release certifier
  root and verify the submitted bundle

## Verifier Outcomes

The verifier should return:

```text
pass
fail
incomplete
unsupported_exclusion
stale_root
artifact_mismatch
policy_mismatch
unauthorized_peer
```

Positive-only provenance is not enough for release readiness when revocation,
withdrawn approval, or active vulnerability facts are part of the policy.

## Source Anchors

- [in-toto candidate](../candidates/in-toto/index.md)
- [Software Provenance sketch](../candidates/software-provenance/index.md)
- [in-toto getting started](https://in-toto.io/docs/getting-started/)
- [in-toto Attestation Framework repository](https://github.com/in-toto/attestation)

