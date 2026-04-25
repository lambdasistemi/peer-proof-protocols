# Proof Request Language

If proofs are pull-oriented, peers need a small language for asking for them.

The request language should describe what the verifier needs, not how the
holder stores its data.

The goal is:

```text
given anchor A and transition T,
return the minimal facts and proofs needed to evaluate T
```

## Design Constraints

A proof request must be:

- specific enough that the holder knows what to disclose
- scoped enough to avoid over-disclosure
- deterministic enough that different holders understand the same request
- explicit about positive and negative evidence
- explicit about freshness and policy
- transport-neutral

It should work over HTTP, wallet-to-wallet messages, DIDComm-like channels,
local files, QR handoff, or any later protocol transport.

## Request Envelope

A request envelope should contain:

```text
request_id
request_version
requester
holder
anchor_identity
claim
transition
policy
freshness
positive_requirements
negative_requirements
disclosure_limits
response_options
expires_at
signature
```

The `holder` may be the producer, claimant, certifier, wallet, agent, or any
party expected to have access to the relevant facts and proof material.

## Anchor Identity

The request must identify the claim-history anchor:

```text
network
owner_public_key
namespace
scope_digest
anchor_token_policy_id
anchor_token_name
validator_version
```

This tells the holder which claim history the verifier will resolve on-chain.
It also lets the verifier reject a response that points to the wrong anchor.

## Claim Or Transition

The request should name the claim or transition being evaluated.

Examples:

```text
claim: artifact D satisfies release policy P
transition: release_candidate -> releasable
claim: shipment S has no active hold under policy P
transition: custody_offered -> custody_accepted
```

The transition matters because it determines both required inclusions and
required exclusions.

## Positive Requirements

Positive requirements ask for facts that must exist.

Example:

```text
include:
  - build_succeeded(artifact D)
  - tests_passed(artifact D)
  - scan_completed(artifact D)
  - publisher_approved(artifact D)
```

Each positive requirement should specify:

```text
fact type
subject
authoring peer
accepted root or root range
required policy version
```

## Negative Requirements

Negative requirements ask for proof that blocking facts do not exist in a
declared committed scope.

Example:

```text
exclude:
  - revoked(artifact D)
  - active_release_hold(artifact D)
  - failed_required_test(artifact D)
  - critical_vulnerability_above_threshold(artifact D)
```

Each negative requirement should specify:

```text
blocking fact type
subject
authoring peer
committed key space
scope
freshness bound
outcome if unsupported
```

The safe default is:

```text
unsupported exclusion -> incomplete
```

Never treat an omitted blocking fact as proof of absence.

## Freshness

Freshness should be phrased against the anchor, not just a holder timestamp.

Examples:

```text
root must be current at transaction submission
root must be published before deadline slot D
root must be no older than policy window W
no later revocation root may affect scope S
```

The response can include an `out_ref` hint, but the verifier should still
resolve the anchor token to the current UTxO.

## Disclosure Limits

The request should state how much the holder may disclose.

Examples:

```text
subject-only
policy-minimal
include redacted payloads
include full payloads
exclude unrelated artifacts
exclude private peer identifiers
```

The holder may refuse if the policy cannot be satisfied inside the requested
disclosure limits.

## Response Envelope

A response should contain:

```text
request_id
response_id
response_version
holder
anchor_identity
verification_context
disclosed_facts
inclusion_proofs
exclusion_proofs
claim_root_proof
claim_history_proof
policy_metadata
unsupported_requirements
current_out_ref_hint
signature
```

If the holder cannot satisfy the request, it should return a structured refusal
or partial response:

```text
unsupported predicate
unsupported exclusion
fact unavailable
root stale
policy unknown
disclosure limit too strict
not authorized
```

## Minimal Example

```json
{
  "request_version": "ppp.proof_request.v1",
  "anchor_identity": {
    "network": "cardano-preview",
    "owner_public_key": "owner-key-hash",
    "namespace": "provenance",
    "scope_digest": "scope-hash",
    "anchor_token_policy_id": "policy-id",
    "anchor_token_name": "token-name"
  },
  "transition": {
    "from": "release_candidate",
    "to": "releasable",
    "policy_id": "release-policy",
    "policy_version": "1"
  },
  "positive_requirements": [
    {
      "fact_type": "build_succeeded",
      "subject": "artifact-digest"
    }
  ],
  "negative_requirements": [
    {
      "fact_type": "active_release_hold",
      "subject": "artifact-digest",
      "outcome_if_unsupported": "incomplete"
    }
  ],
  "freshness": {
    "mode": "current_at_submission"
  }
}
```

## Why This Belongs In PPP

Anchors make roots discoverable. They do not tell holders what proof to
produce.

The proof request language is the bridge between:

- a verifier's protocol transition
- a holder's private fact store
- the public claim-history anchor
- a portable proof bundle

Without this language, proof distribution becomes ad hoc and push-heavy. With
it, proof generation can be demand-driven, minimal, and tied to explicit
transition evidence contracts.
