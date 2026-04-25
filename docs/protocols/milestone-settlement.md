# Milestone Settlement Blueprint

This blueprint starts from recurring service, construction, delivery,
procurement, and implementation workflows where payment depends on evidence
from more than one party.

## Scenario

A vendor claims that a milestone is complete and payable. The client, reviewer,
QA team, delivery platform, escrow, or finance process needs to verify that the
claim is supported.

The relevant facts are not all owned by one party:

- vendor owns delivery facts
- reviewer or QA owns test and acceptance evidence
- client owns acceptance, rejection, and dispute facts
- escrow or finance system owns payment authorization facts
- auditor may certify the derived payable result

## Value Proposition

The verifier should not rely on a platform dashboard or one PDF export to decide
whether payment is due.

PPP adds value when the payment decision can be checked from:

- vendor delivery inclusion
- reviewer or QA approval inclusion
- client acceptance inclusion
- exclusion of active dispute, rejection, hold, or superseding change request
- freshness before payment execution
- an optional certification fact for `payable`

## Shared Claim

One useful first claim is:

```text
milestone M is payable under contract or policy P
```

This is deliberately narrow. It does not say the entire contract is complete or
that no legal claim exists outside the scoped evidence.

## Peer-Owned Facts

Examples:

```text
vendor: delivered(milestone M, artifact A)
vendor: completion_claimed(milestone M)
qa: tests_passed(milestone M, suite S)
reviewer: reviewed(milestone M, checklist C)
client: accepted(milestone M)
finance: payment_authorized(milestone M, invoice I)
auditor: payable_certified(M, policy P, input_bundle_digest B)
```

Blocking facts:

```text
vendor excludes delivery_withdrawn(milestone M)
qa excludes failed_required_test(milestone M)
client excludes disputed(milestone M)
client excludes rejected(milestone M)
client excludes active_hold(milestone M)
finance excludes payment_blocked(invoice I)
```

## Claim Authoring Flow

Milestone settlement naturally uses both authoring requests and proof requests.

Authoring requests:

```text
vendor asks QA to score or approve artifact A under checklist C
vendor asks client to accept or reject milestone M
client asks reviewer to certify completion under policy P
```

The response creates new peer-authored state. It may be an approval, rejection,
score, dispute, or abstention.

Proof requests happen later:

```text
finance knows claim payable(M)
finance asks holders for current proofs that the required claims still stand
holders return proof bundles or current statuses
finance verifies before payment
```

## Proof Bundle

A payable proof bundle should carry:

- delivery fact and inclusion proof
- QA or reviewer facts and inclusion proofs
- client acceptance fact and inclusion proof
- exclusion proofs for dispute, rejection, active hold, and payment block
- policy id and version
- peer root references or anchored claim-history references
- optional auditor certification fact
- optional audit-through input bundle

## Certification Fact

An auditor, client, or escrow agent can publish:

```text
payable(milestone M, policy P, input_bundle_digest B, amount A)
```

The certification must pin:

- milestone id
- contract, work order, or policy reference
- result and optional payable amount
- input bundle digest
- input peer roots or checkpoints
- certifier identity
- certification epoch or ledger position

The amount should be treated as a parameterized claim. It is only meaningful
under the pinned contract, policy, currency, and scope.

## Infrastructure Path

Start off-chain:

- vendor, QA, client, and finance sign roots over their own workflow facts
- proof bundles are requested only when a payment, audit, or dispute needs them
- browser or CLI verifier checks the bundle before payment approval

Move roots on-chain when escrow or smart-contract settlement consumes the
claim:

- vendor, client, and certifier anchors can be reference inputs
- the payment contract verifies the disclosed facts and exclusions against the
  current claim histories
- the chain enforces settlement over commitments, not the private project
  database

## Verifier Outcomes

The verifier should return:

```text
payable
not_payable
incomplete
disputed
rejected
active_hold
stale_root
policy_mismatch
amount_mismatch
unsupported_exclusion
```

Payment should not proceed on positive delivery and QA facts alone if the policy
requires proof that there is no active dispute, rejection, or hold.

## Source Anchors

- [Milestone Settlement sketch](../candidates/milestone-settlement/index.md)
- [Compliance Audit-Signoff candidate](../candidates/compliance-audit-signoff/index.md)
- [Dispute Resolution candidate](../candidates/dispute-resolution/index.md)
- [Composition and Certification](../concepts/composition-and-certification.md)

