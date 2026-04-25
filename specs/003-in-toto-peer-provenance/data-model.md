# Data Model: in-toto Peer Provenance Proofs

## 1. ProvenancePeer

- **Purpose**: Identifies one project owner, functionary, service, publisher,
  or certifier that owns provenance facts and signs roots over its own state.
- **Fields**:
  - `peer_id`
  - `display_name`
  - `role`
  - `trust_anchor`
  - `root_publication_policy`
  - `supported_fact_types`
  - `supported_proof_types`
- **Validation**:
  - `peer_id` is stable across facts, roots, bundles, and certifications.
  - `role` must not imply authority over another peer's evidence.
  - `supported_proof_types` states whether exclusion proofs are supported.

## 2. ArtifactSubject

- **Purpose**: Identifies the software artifact or source subject the claim is
  about.
- **Fields**:
  - `name`
  - `digest`
  - `uri` (optional)
  - `subject_kind`
  - `content_type` (optional)
- **Validation**:
  - `digest` is required for artifact verification.
  - Subject matching is digest-first.
  - `subject_kind` is `source`, `build_output`, `package`, `container`,
    `release_asset`, or a versioned extension.

## 3. PeerRootCheckpoint

- **Purpose**: Records a signed commitment by one peer over a declared
  provenance state scope.
- **Fields**:
  - `checkpoint_id`
  - `peer_id`
  - `root_id`
  - `root_value`
  - `root_semantics`
  - `scope`
  - `issued_at`
  - `valid_from`
  - `valid_until` (optional)
  - `key_id`
  - `signature`
- **Validation**:
  - `root_semantics` is one of `step_history`, `active_artifact_state`,
    `revocation_index`, `exclusion_index`, `certification_state`, `combined`,
    or a versioned extension.
  - `scope` declares artifact namespace, fact types, policy boundaries, and
    predicate families.
  - `signature` covers the checkpoint envelope, not only the root value.

## 4. AttestationReference

- **Purpose**: Links a canonical peer fact to in-toto evidence.
- **Fields**:
  - `reference_id`
  - `evidence_kind`
  - `predicate_type` (optional)
  - `statement_type` (optional)
  - `envelope_digest` (optional)
  - `bundle_line_digest` (optional)
  - `layout_digest` (optional)
  - `link_digest` (optional)
  - `embedded_excerpt` (optional)
- **Validation**:
  - `evidence_kind` is `layout`, `link`, `statement`, `envelope`, `bundle`,
    `predicate`, or a versioned extension.
  - At least one digest or embedded excerpt reference is required.
  - Predicate type must be preserved when the evidence is predicate-specific.

## 5. CanonicalProvenanceFact

- **Purpose**: The normalized peer-authored fact committed as a leaf.
- **Fields**:
  - `fact_id`
  - `fact_type`
  - `canonical_version`
  - `peer_id`
  - `artifact_subject`
  - `step_name` (optional)
  - `predicate_type` (optional)
  - `policy_id` (optional)
  - `policy_version` (optional)
  - `fact_time`
  - `commitment_time`
  - `status`
  - `payload`
  - `payload_hash`
  - `attestation_refs`
- **Validation**:
  - `fact_id` is deterministic for the same canonical fact.
  - `payload_hash` is derived from canonical payload bytes.
  - `peer_id` must match the root checkpoint used to prove the fact.
  - `artifact_subject.digest` must match the artifact scope of the claim unless
    the fact is explicitly policy-level or layout-level.

## 6. BlockingCondition

- **Purpose**: Represents a peer-authored fact that weakens or defeats the
  provenance claim.
- **Fields**:
  - `condition_type`
  - `artifact_subject`
  - `peer_id`
  - `scope`
  - `status`
  - `effective_from`
  - `effective_until` (optional)
  - `source_fact_id`
- **Validation**:
  - `condition_type` is one of `revocation`, `failed_test`,
    `vulnerability_threshold_breach`, `compromised_key`, `release_hold`,
    `policy_exception`, `predicate_deprecated`, or a versioned extension.
  - Active blocking conditions force `fail`, `conflicted`, or `superseded`
    according to policy.

## 7. InclusionProof

- **Purpose**: Proves that a canonical provenance fact is included under a peer
  root checkpoint.
- **Fields**:
  - `proof_type`
  - `fact_id`
  - `leaf_hash`
  - `root_id`
  - `root_value`
  - `path`
  - `algorithm`
- **Validation**:
  - `leaf_hash` recomputes from the canonical fact.
  - `path` recomputes to `root_value`.
  - `root_id` references a `PeerRootCheckpoint` in the same bundle.

## 8. ExclusionProof

- **Purpose**: Proves absence of a revocation, hold, failed result, or other
  blocking condition within a declared committed key space.
- **Fields**:
  - `proof_type`
  - `query_key`
  - `query_scope`
  - `root_id`
  - `root_value`
  - `neighbor_or_range_evidence`
  - `algorithm`
- **Validation**:
  - `query_scope` must be covered by the checkpoint scope.
  - Exclusion proof is valid only for root semantics that support the declared
    key space.
  - Missing or unsupported exclusion proof yields `unsupported` or
    `incomplete`, not `pass`.

## 9. ProvenanceProofBundle

- **Purpose**: The portable artifact carried from claimer to verifier.
- **Fields**:
  - `bundle_id`
  - `bundle_version`
  - `claim`
  - `peers`
  - `root_checkpoints`
  - `facts`
  - `attestation_refs`
  - `inclusion_proofs`
  - `exclusion_proofs`
  - `coverage`
  - `certifications`
  - `created_at`
- **Validation**:
  - Every proof references a root checkpoint in the bundle.
  - Every fact references a declared peer.
  - `coverage` states artifact subject, peer scope, predicate families, policy,
    time range, and limits.
  - The bundle is verifier-complete except for external trust anchors.

## 10. SharedProvenanceClaim

- **Purpose**: The bounded cross-peer statement evaluated by the verifier.
- **Fields**:
  - `claim_type`
  - `artifact_subject`
  - `required_peers`
  - `policy_id`
  - `policy_version`
  - `freshness_requirement`
  - `required_predicate_types`
  - `required_negative_claims`
  - `expected_outcome`
- **Validation**:
  - `claim_type` for MVP is `ppp.in_toto.provenance_bundle.v1`.
  - `required_peers` includes at least two peer roles.
  - Policy determines how conflicts, missing peers, stale roots, and
    unsupported predicates are classified.

## 11. CertificationFact

- **Purpose**: A derived peer-authored fact created after verifying a bundle and
  applying a policy.
- **Fields**:
  - `certification_id`
  - `fact_type`
  - `certifier_peer_id`
  - `artifact_subject`
  - `policy_id`
  - `policy_version`
  - `result`
  - `input_bundle_digest`
  - `input_root_refs`
  - `input_claim_refs`
  - `verification_mode`
  - `certified_at`
- **Validation**:
  - `input_bundle_digest` identifies the exact verified input bundle.
  - `input_root_refs` includes every peer root relied on by the result.
  - `verification_mode` is `trust_certifier`, `audit_through`, or `mixed`.

## 12. VerificationResult

- **Purpose**: Explains the outcome of checking a provenance proof bundle.
- **Fields**:
  - `bundle_id`
  - `claim_type`
  - `outcome`
  - `reason_codes`
  - `verified_roots`
  - `failed_proofs`
  - `unsupported_predicates`
  - `unsupported_claims`
  - `stale_inputs`
  - `conflicts`
  - `evaluated_at`
- **Validation**:
  - `outcome` is one of `pass`, `fail`, `incomplete`, `stale`, `conflicted`,
    `unsupported`, or `superseded`.
  - Every non-pass outcome includes at least one `reason_code`.
  - `pass` requires all required peers, roots, proofs, freshness checks,
    predicate interpretations, and negative claims to evaluate successfully.
