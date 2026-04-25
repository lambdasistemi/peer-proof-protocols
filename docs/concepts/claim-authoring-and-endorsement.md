# Claim Authoring And Endorsement

Proof requests are not the only request type.

There is another protocol lane:

```text
please author, complete, score, endorse, dispute, or certify this claim
```

This is not proof revalidation. It is a request for a peer to create a new
claim in its own state.

## Partial Claims Are Templates

A partial claim is not yet a claim.

It is a template or prompt that says:

```text
subject: artifact A
predicate: quality_score
allowed value: integer 0..10
rubric: release-quality-v1
parameters: platform, test suite, deadline
```

The recipient may complete it, refuse it, ignore it, or publish a different
claim.

Only the recipient's authored state can make the completed claim attributable
to that recipient.

## What The Verifier Can Score

If Alice asks Bob:

```text
how do you rate artifact A from 0 to 10 under rubric R?
```

and Bob answers:

```text
7
```

the provable fact is not that artifact A is objectively a 7.

The provable fact is:

```text
Bob declared score 7 for artifact A under rubric R
```

That distinction matters. PPP can prove authorship, current support, revocation
state, and absence of blocking facts. It cannot make subjective or ambiguous
claims objectively true.

## Endorsement Facts

Endorsement is a claim authored by the endorser.

Examples:

```text
Bob endorses Alice claim C under policy P
Bob disputes Alice claim C under policy P
Bob scores subject S as 7 under rubric R
Bob certifies bundle B satisfies rule R
Bob abstains from claim C
```

The endorsement payload should pin:

- referenced claim digest or template digest
- relation kind such as `endorse`, `dispute`, `score`, `certify`, or `abstain`
- subject identifier
- value or result
- value space, rubric, policy, or vocabulary version
- parameters that affect interpretation
- author identity
- anchor identity

Without the pinned value space and parameters, later aggregation is unsafe.
`blue`, `azul`, and `#0000ff` may be equivalent in one vocabulary and different
in another.

## Claim Authoring Request

A claim authoring request should contain:

```text
request_id
request_version
requester
requested_author
template_digest
claim_template
allowed_relations
allowed_values
required_parameters
rubric_or_policy
vocabulary
expires_at
desired_response_mode
signature
```

The response, if accepted, should contain:

```text
request_id
response_id
author
anchor_identity
completed_claim
template_digest
claim_digest
claim_status
optional inclusion proof
optional exclusion proof
optional current out_ref hint
signature
```

The response may omit proofs if the claim has not yet been anchored. In that
case the response is only a signed message. It becomes signed state only after
the author includes the completed claim in its Merkle-authenticated state.

## Lifecycle

The lifecycle is:

```text
requester sends partial claim or endorsement prompt
recipient decides whether to participate
recipient completes the claim under pinned parameters
recipient publishes it in its own state
recipient may return a proof bundle
later verifier asks for proof that recipient still claims it
```

So the authoring request creates or proposes new facts. The proof request later
checks whether those facts are still supported by current state.

## Why Merkle State Helps

For authored opinions, scores, and endorsements, Merkle state does not make the
content objectively true.

It helps because a peer can later prove:

- it included the declaration in its own committed state
- it still stands behind the declaration
- it revoked or superseded the declaration
- it did not include a conflicting declaration in a committed scope
- a certification fact was derived from a pinned input bundle

That is the useful trust layer: not truth by authority, but accountable,
versioned, peer-owned state.

## Boundary With Proof Requests

Use a claim authoring request when the requester wants the recipient to create a
new position.

Use a proof request when the requester already knows the position and needs
current evidence that the author still claims it.

The two compose:

```text
authoring request -> completed claim -> anchored state -> proof request
```

