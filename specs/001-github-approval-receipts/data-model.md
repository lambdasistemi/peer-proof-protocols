# Data Model: GitHub Approval Receipts

## 1. PullRequestSubject

- **Purpose**: Identifies the GitHub pull request the receipt is about.
- **Fields**:
  - `host`
  - `owner`
  - `repo`
  - `pull_number`
  - `base_ref`
  - `head_ref`
  - `head_sha`
  - `merged`
  - `merge_commit_sha` (optional)
- **Validation**:
  - `host`, `owner`, `repo`, and `pull_number` are required.
  - `merge_commit_sha` is only present when `merged = true`.

## 2. RuleContextSnapshot

- **Purpose**: Captures the review-governing rule state used to interpret
  approval.
- **Fields**:
  - `subject_ref`
  - `captured_at`
  - `required_approving_review_count`
  - `dismiss_stale_reviews`
  - `require_last_push_approval`
  - `require_code_owner_reviews`
  - `bypass_actors` (possibly redacted or partial)
  - `source_kind` (`rules_api`, `branch_protection`, or `combined`)
  - `completeness`
- **Validation**:
  - `completeness` must be `complete` or `partial`.
  - Missing rule visibility forces the final claim toward `incomplete`.

## 3. ReviewFact

- **Purpose**: Represents one GitHub review or review-related invalidation fact.
- **Fields**:
  - `review_id`
  - `actor_login`
  - `state`
  - `submitted_at`
  - `commit_id` (optional)
  - `dismissed_at` (optional)
  - `dismissal_actor` (optional)
  - `source`
- **Validation**:
  - `state` distinguishes approval, change request, comment-only, and
    dismissal-derived facts.
  - Dismissal metadata attaches only when a review has been dismissed.

## 4. MergeContext

- **Purpose**: Describes whether a merged result is equivalent to the reviewed
  state.
- **Fields**:
  - `merged`
  - `merge_method`
  - `merge_commit_sha`
  - `head_sha_at_receipt`
  - `equivalence_status`
  - `equivalence_reason`
- **Validation**:
  - `equivalence_status` is `equivalent`, `non_equivalent`, or `not_applicable`.
  - Rebase/squash merges can still be supported, but may require
    `non_equivalent` or `incomplete` if equivalence cannot be established.

## 5. CanonicalFact

- **Purpose**: The normalized leaf input to the receipt Merkle tree.
- **Fields**:
  - `fact_id`
  - `fact_type`
  - `canonical_version`
  - `payload`
  - `payload_hash`
- **Validation**:
  - `fact_id` is deterministic for the same source fact.
  - `payload_hash` is derived from the canonical payload bytes.

## 6. ApprovalClaim

- **Purpose**: The exact bounded statement the receipt verifies.
- **Fields**:
  - `claim_type` (`github.pr_approval.v1`)
  - `subject`
  - `claim_mode` (`pre_merge`, `post_merge`, or `combined`)
  - `classification` (`valid`, `discrepant`, `incomplete`)
  - `reason_codes`
  - `evaluated_at`
- **Validation**:
  - Classification is required.
  - `reason_codes` must explain every non-valid outcome.

## 7. ApprovalReceiptBundle

- **Purpose**: The portable artifact exported to users and verifiers.
- **Fields**:
  - `receipt_id`
  - `receipt_version`
  - `claim`
  - `evidence_root`
  - `facts`
  - `trust_boundary`
  - `signature`
  - `registry_checkpoint` (optional)
- **Validation**:
  - `facts` must be sufficient to recompute `evidence_root`.
  - `signature` signs the receipt envelope, not just the root.

## 8. RegistryCheckpoint

- **Purpose**: Aggregates receipt roots into a registry-level checkpoint.
- **Fields**:
  - `checkpoint_id`
  - `issued_at`
  - `receipt_roots`
  - `checkpoint_root`
  - `signature`
- **Validation**:
  - `checkpoint_root` is reproducible from the ordered `receipt_roots`.
  - Checkpoints are append-only after publication.
