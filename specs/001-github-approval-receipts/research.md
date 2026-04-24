# Research: GitHub Approval Receipts

## Decision: Start with pull request approval governance, not releases or comments

- **Decision**: The first claim type is `github.pr_approval.v1`.
- **Rationale**: GitHub review state is nuanced and easy to misread. Protected
  branches and rulesets can dismiss stale approvals, require approval from
  someone other than the last pusher, and allow bypass actors. By contrast,
  GitHub now supports immutable releases, which reduces the immediate trust gap
  for release assets.
- **Alternatives considered**:
  - Release receipts first: deferred because immutable releases already cover
    tag and asset mutability.
  - Comment or issue notarization first: deferred because GitHub already shows
    some edit history, but the business wedge is weaker than approval receipts.

## Decision: Use official GitHub REST interfaces for MVP issuance

- **Decision**: MVP receipt issuance fetches current evidence on demand from
  GitHub REST endpoints for pull requests, reviews, rules, and protected
  branches.
- **Rationale**: This satisfies the constitution requirement for official
  interfaces while avoiding a mandatory always-on sync service for v1.
- **Alternatives considered**:
  - GraphQL-first issuance: rejected because the needed claim is easier to
    explain and fixture with explicit REST resources.
  - Webhook-only state: rejected because first issuance should work without a
    preexisting ingest history.

## Decision: Treat webhooks as an optimization, not a hard dependency

- **Decision**: GitHub webhook events such as `pull_request_review` and
  `pull_request` are optional for later incremental sync and discrepancy alerts.
- **Rationale**: Webhooks are useful for change detection, but the first
  product must generate a receipt from the current authoritative GitHub state.
- **Alternatives considered**:
  - Mandatory webhook ingestion before any receipt can be issued: rejected as
    too heavy for the first customer and too brittle for ad hoc verification.

## Decision: Do not make audit logs foundational

- **Decision**: GitHub audit logs are optional corroboration only and are not
  required to verify a receipt.
- **Rationale**: GitHub documents that organization audit logs are owner-only
  and retain events for 180 days. That makes them unsuitable as the core trust
  substrate for portable long-lived receipts.
- **Alternatives considered**:
  - Audit-log-centric proof model: rejected because access and retention are too
    limited for the intended verifier audience.

## Decision: Classification is ternary, not binary

- **Decision**: Receipt verification returns `valid`, `discrepant`, or
  `incomplete`.
- **Rationale**: GitHub visibility and governance can be partial. The system
  must distinguish "evidence contradicts the claim" from "evidence is missing
  or insufficient."
- **Alternatives considered**:
  - Pass/fail only: rejected because it collapses missing evidence into a false
    claim of certainty.

## Decision: Use deterministic canonical JSON with Merkle receipts

- **Decision**: Canonical facts are normalized into deterministic JSON, hashed
  with domain-separated SHA-256 leaf hashing, and combined into a Merkle root.
- **Rationale**: JSON artifacts are portable across API, CLI, and browser
  verification surfaces. SHA-256 is broadly supported and easy to verify in
  browsers and Node.
- **Alternatives considered**:
  - CBOR-first encoding: rejected for v1 because the human-auditable export
    story is weaker.
  - Ad hoc JSON stringification: rejected because deterministic ordering and
    versioning would be too fragile.

## Decision: Sign receipts and checkpoint receipt roots

- **Decision**: Each receipt bundle is signed with an Ed25519 registry key, and
  receipt roots can be grouped into daily registry checkpoints.
- **Rationale**: This gives immediate portability and tamper evidence without
  introducing chain anchoring in the first release.
- **Alternatives considered**:
  - No signatures: rejected because anyone could repack a receipt bundle.
  - Blockchain anchoring in v1: rejected because it would dominate scope before
    the GitHub wedge is proven.

## Decision: TypeScript monorepo for shared verification

- **Decision**: Use TypeScript on Node.js with a shared verifier core consumed
  by the API, CLI, and browser verifier.
- **Rationale**: The same receipt and canonicalization logic needs to run in
  browser and server contexts. TypeScript minimizes duplicated logic.
- **Alternatives considered**:
  - Separate server and browser implementations: rejected because parity would
    become a new trust problem.

## Official GitHub sources used

- Pull request review semantics:
  https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-request-reviews
- Protected branch behavior:
  https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches
- Rulesets behavior:
  https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets
- Rules API:
  https://docs.github.com/en/rest/repos/rules
- Protected branch API:
  https://docs.github.com/en/rest/branches/branch-protection
- Webhook events and payloads:
  https://docs.github.com/en/webhooks/webhook-events-and-payloads
- Merge-method behavior:
  https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/about-merge-methods-on-github
- Immutable releases:
  https://docs.github.com/code-security/supply-chain-security/understanding-your-software-supply-chain/immutable-releases
- Organization audit log retention:
  https://docs.github.com/articles/reviewing-the-audit-log-for-your-organization
