# Implementation Plan: [FEATURE]

**Branch**: `[###-feature-name]` | **Date**: [DATE] | **Spec**: [link]
**Input**: Feature specification from `/specs/[###-feature-name]/spec.md`

**Note**: This template is filled in by the `/speckit.plan` command. See
`.specify/templates/plan-template.md` for the execution workflow.

## Status

**Completed**: [What is already decided or finished]  
**Current**: [What phase is in progress]  
**Blockers**: [Open questions, missing inputs, or "None"]

## Summary

[Extract from feature spec: primary requirement + technical approach from
research]

## Technical Context

**Protocol / Domain**: [e.g., milestone settlement, custody handoff, build
provenance]  
**Peer Set / Roles**: [peers and their responsibilities]  
**Peer-Owned Facts**: [what each peer commits to and is authoritative for]  
**Shared Claim / Outcome**: [what cross-peer statement the verifier evaluates]  
**Proof-Carrying Message**: [portable bundle exchanged between peers or shown
to verifiers]  
**Composition Model**: [base facts only, or base facts -> derived claim ->
certified fact]  
**Audit Mode**: [trust certifier, audit through, or mixed]  
**Verification Surface**: [browser, CLI, API, or mixed]  
**Witness / Publication Layer**: [witnesses, public feeds, settlement layer,
or N/A]  
**Merkle Substrate**: [MPF/MPFS model, per-peer namespaces, root publication
policy]  
**Implementation Stack**: [languages, runtimes, frameworks or NEEDS
CLARIFICATION]  
**Storage / Anchoring**: [peer stores, witness logs, chain anchor, or N/A]  
**Testing Strategy**: [contract, golden, replay, integration, verifier tests]  
**Constraints**: [freshness, privacy, offline exchange, peer liveness,
revocation, dispute windows]  
**Scale / Scope**: [peers, proofs per claim, root cadence, throughput]

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- [ ] The feature targets a peer-only protocol, not a centralized oracle or
      coordinator product.
- [ ] Peer boundaries are explicit: what each peer authors, signs, stores, and
      proves is documented.
- [ ] Each peer has its own root lifecycle and proof publication model.
- [ ] The design explains why authenticated state and Merkle proofs add value
      beyond detached signatures.
- [ ] Canonicalization, ordering, normalization, and versioning are defined
      well enough to reproduce identical leaves, roots, and proof bundles from
      identical peer inputs.
- [ ] A proof-carrying interaction and an independent partial-verification path
      are defined.
- [ ] If the feature promotes derived claims into certified facts, the
      certification payload, policy/rule identifiers, and downstream audit mode
      are defined.
- [ ] Any witness, relay, or settlement layer is narrow, passive, and
      non-authoritative.
- [ ] Key custody, freshness limits, dispute modes, and degraded behavior are
      explicitly documented.
- [ ] Golden fixtures and negative verification cases are planned for each new
      or changed cross-peer claim.

## Project Structure

### Documentation (this feature)

```text
specs/[###-feature]/
├── plan.md              # This file (/speckit.plan command output)
├── research.md          # Phase 0 output (/speckit.plan command)
├── data-model.md        # Phase 1 output (/speckit.plan command)
├── quickstart.md        # Phase 1 output (/speckit.plan command)
├── contracts/           # Phase 1 output (/speckit.plan command)
└── tasks.md             # Phase 2 output (/speckit.tasks command - NOT created by /speckit.plan)
```

### Source Code (repository root)

```text
protocols/
├── [protocol]/
│   ├── rules/
│   ├── messages/
│   └── verification/
peers/
├── [peer]/
│   ├── canonical/
│   ├── proofs/
│   ├── certifications/
│   └── publication/
witnesses/
├── [component]/
schemas/
├── canonical/
├── bundles/
└── certifications/
verifiers/
├── browser/
├── cli/
└── api/
docs/
tests/
├── contract/
├── golden/
├── integration/
└── replay/
```

**Structure Decision**: [Document the selected structure and reference the real
directories captured above]

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| [e.g., coordinator decides peer facts] | [current need] | [why peer-owned state was insufficient] |
| [e.g., opaque certification with no audit path] | [specific need] | [why pinned inputs and audit-through were infeasible] |
| [e.g., signatures only, no Merkle state] | [specific blocker] | [why authenticated state continuity is still required] |
