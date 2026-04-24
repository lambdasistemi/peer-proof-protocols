# Implementation Plan: GitHub Approval Receipts

**Branch**: `001-github-approval-receipts` | **Date**: 2026-04-23 | **Spec**:
[spec.md](/code/peer-proof-protocols/specs/001-github-approval-receipts/spec.md)
**Input**: Feature specification from
`/specs/001-github-approval-receipts/spec.md`

**Note**: This plan covers Phase 0 research and Phase 1 design only.

## Status

**Completed**: Feature specification, GitHub trust-gap analysis, planning
scaffold  
**Current**: Technical design for approval receipt issuance and verification  
**Blockers**: None

## Summary

Build a trust overlay for GitHub pull request approvals. The first release
issues a portable approval receipt for one pull request, using official GitHub
REST data to capture review state, rule context, and merge equivalence, then
packages that evidence into a deterministic Merkle receipt that can be verified
independently.

## Technical Context

**Target SaaS / Workflow**: GitHub pull request review and merge governance  
**Official Interface**: GitHub REST endpoints for pull requests, reviews,
rules, and protected branches; GitHub webhook payloads are optional for later
incremental sync and not required for MVP issuance  
**Canonical Inputs**: Pull request snapshot, review facts, rule context, merge
context, and visibility disclaimers collected at issuance time  
**Receipt / Checkpoint Artifact**: Signed JSON approval receipt bundle with
receipt Merkle root, evidence facts, classification, and optional registry
checkpoint reference  
**Verification Surface**: Mixed: API issuance, shared verifier core, CLI
verification first, browser verifier second using the same receipt bundle  
**Implementation Stack**: TypeScript 5.x, Node.js 22 LTS, Fastify API,
Octokit, Zod, shared verification package, lightweight browser verifier  
**Storage / Anchoring**: SQLite for MVP receipt and checkpoint persistence,
JSON receipt exports on demand, Ed25519 registry signatures, no chain anchoring
in v1  
**Testing Strategy**: GitHub contract fixtures, golden canonicalization tests,
receipt replay tests, verifier parity tests between CLI and browser, negative
cases for stale, dismissed, bypassed, and non-equivalent merges  
**Constraints**: GitHub.com first, official interfaces only, no audit-log hard
dependency, private repos require sufficient read/admin visibility, incomplete
classification required when rule visibility or history is partial  
**Scale / Scope**: Single GitHub host in v1, one receipt per requested pull
request, tens of receipts per repo per day, daily registry checkpoints

**Language/Version**: TypeScript 5.x on Node.js 22 LTS  
**Primary Dependencies**: Fastify, Octokit, Zod, Vitest  
**Storage**: SQLite + exported JSON receipt bundles  
**Project Type**: Monorepo API + verifier packages

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- [x] The feature augments an existing third-party workflow and does not replace
      it as the system of record.
- [x] All upstream inputs come from official public APIs, webhooks, or export
      surfaces, with those sources captured in `research.md`.
- [x] Canonicalization, ordering, normalization, and versioning are defined
      well enough to reproduce identical checkpoints from identical inputs.
- [x] A portable receipt or checkpoint artifact is defined, along with an
      independent verification path.
- [x] Trust boundaries, custody assumptions, freshness limits, and degraded
      modes are explicitly documented.
- [x] Golden fixtures and negative verification cases are planned for each new
      or changed trust claim.

## Project Structure

### Documentation (this feature)

```text
specs/001-github-approval-receipts/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   ├── receipt-bundle.schema.json
│   └── registry-api.yaml
└── tasks.md
```

### Source Code (repository root)

```text
adapters/
└── github/
    ├── client/
    ├── canonical/
    └── ingest/
registry/
├── api/
├── checkpoints/
├── receipts/
└── publication/
schemas/
├── canonical/
└── receipts/
verifiers/
├── browser/
├── cli/
└── shared/
tests/
├── contract/
├── golden/
├── integration/
└── replay/
```

**Structure Decision**: Keep one repository with GitHub-specific adapter code,
shared receipt and verification logic, an issuance API, and two verifier
surfaces. Receipt logic lives in shared packages; the API and browser remain
thin wrappers over that core.

## Complexity Tracking

No constitution violations require justification in this plan.

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| None | N/A | N/A |
