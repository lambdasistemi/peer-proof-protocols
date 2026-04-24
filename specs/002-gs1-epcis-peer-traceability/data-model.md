# Data Model: GS1 EPCIS Peer Traceability Proofs

## 1. TraceabilityPeer

- **Purpose**: Identifies one organization or actor that owns traceability
  facts and signs roots over its own state.
- **Fields**:
  - `peer_id`
  - `display_name`
  - `role`
  - `trust_anchor`
  - `root_publication_policy`
  - `supported_fact_types`
  - `supported_proof_types`
- **Validation**:
  - `peer_id` is stable across all facts, roots, and bundles.
  - `role` must not imply authority over facts authored by another peer.
  - `supported_proof_types` must state whether exclusion proofs are supported.

## 2. PeerRootCheckpoint

- **Purpose**: Records a signed commitment by one peer over a declared state
  scope.
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
  - `root_semantics` is one of `event_history`, `active_state`,
    `exclusion_index`, `combined`, or a versioned extension.
  - `scope` declares subject namespace, fact types, and policy boundaries.
  - `signature` covers the checkpoint envelope, not only the root value.

## 3. CanonicalTraceabilityFact

- **Purpose**: The normalized peer-authored fact committed as a leaf.
- **Fields**:
  - `fact_id`
  - `fact_type`
  - `canonical_version`
  - `peer_id`
  - `subject`
  - `event_id` (optional)
  - `event_time` (optional)
  - `commitment_time`
  - `biz_step` (optional)
  - `disposition` (optional)
  - `status`
  - `payload`
  - `payload_hash`
- **Validation**:
  - `fact_id` is deterministic for the same canonical fact.
  - `payload_hash` is derived from canonical payload bytes.
  - `peer_id` must match the root checkpoint used to prove the fact.
  - EPCIS-derived vocabulary values carry their vocabulary version.

## 4. BlockingCondition

- **Purpose**: Represents a peer-authored fact that weakens or defeats the
  shared traceability claim.
- **Fields**:
  - `condition_type`
  - `subject`
  - `peer_id`
  - `scope`
  - `status`
  - `effective_from`
  - `effective_until` (optional)
  - `source_fact_id`
- **Validation**:
  - `condition_type` is one of `hold`, `recall`, `dispute`, `rejection`,
    `exception`, `revocation`, or a versioned extension.
  - Active blocking conditions force the shared claim to `fail` or
    `conflicted` according to policy.

## 5. InclusionProof

- **Purpose**: Proves that a disclosed canonical fact is included under a peer
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

## 6. ExclusionProof

- **Purpose**: Proves absence of a key or subject-condition pair within a
  declared committed key space.
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

## 7. TraceabilityProofBundle

- **Purpose**: The portable artifact carried from claimer to verifier.
- **Fields**:
  - `bundle_id`
  - `bundle_version`
  - `claim`
  - `peers`
  - `root_checkpoints`
  - `facts`
  - `inclusion_proofs`
  - `exclusion_proofs`
  - `coverage`
  - `certifications`
  - `created_at`
- **Validation**:
  - Every proof references a root checkpoint in the bundle.
  - Every fact references a declared peer.
  - `coverage` states what subjects, fact types, peer scopes, and time ranges
    are included and excluded.
  - The bundle is verifier-complete except for external trust anchors.

## 8. SharedTraceabilityClaim

- **Purpose**: The bounded cross-peer statement evaluated by the verifier.
- **Fields**:
  - `claim_type`
  - `subject`
  - `required_peers`
  - `policy_id`
  - `policy_version`
  - `freshness_requirement`
  - `required_negative_claims`
  - `expected_outcome`
- **Validation**:
  - `claim_type` for MVP is `ppp.gs1_epcis.traceability_segment.v1`.
  - `required_peers` includes at least two peer roles.
  - Policy determines how conflicts, missing peers, and stale roots are
    classified.

## 9. CertificationFact

- **Purpose**: A derived peer-authored fact created after verifying a bundle and
  applying a policy.
- **Fields**:
  - `certification_id`
  - `fact_type`
  - `certifier_peer_id`
  - `subject`
  - `policy_id`
  - `policy_version`
  - `result`
  - `input_bundle_digest`
  - `input_root_refs`
  - `input_claim_refs`
  - `verification_mode`
  - `certified_at`
- **Validation**:
  - `input_bundle_digest` must identify the exact verified input bundle.
  - `input_root_refs` must include every peer root relied on by the result.
  - `verification_mode` is `trust_certifier`, `audit_through`, or `mixed`.

## 10. VerificationResult

- **Purpose**: Explains the outcome of checking a traceability proof bundle.
- **Fields**:
  - `bundle_id`
  - `claim_type`
  - `outcome`
  - `reason_codes`
  - `verified_roots`
  - `failed_proofs`
  - `unsupported_claims`
  - `stale_inputs`
  - `conflicts`
  - `evaluated_at`
- **Validation**:
  - `outcome` is one of `pass`, `fail`, `incomplete`, `stale`, `conflicted`,
    `unsupported`, or `superseded`.
  - Every non-pass outcome must include at least one `reason_code`.
  - `pass` requires all required peers, roots, proofs, freshness checks, and
    negative claims to evaluate successfully.
