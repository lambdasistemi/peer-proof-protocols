# Feature Specification: GitHub Approval Receipts

**Feature Branch**: `001-github-approval-receipts`  
**Created**: 2026-04-23  
**Status**: Draft  
**Input**: User description: "Create GitHub pull request approval receipts that
prove who approved which commit under which repository rules and detect stale,
dismissed, bypassed, or rewritten approval states."

## System Context *(mandatory)*

**Existing Application**: GitHub pull request review and merge workflow  
**Protected Workflow**: Reviewing, approving, and merging pull requests into
branches with review requirements  
**Trust Problem**: GitHub shows the current state of reviews, branch rules, and
merge results, but teams lack a portable receipt proving which reviewable
change set was approved, which rules were in effect, whether those approvals
later became stale or were dismissed, and whether the final merged outcome is
still equivalent to what was approved  
**Primary Artifact**: Pull request approval receipt bundle  
**Verification Audience**: Release managers, auditors, clients, vendors,
security reviewers, and repository maintainers who need independent proof of
approval state  
**Non-Goals**: Replacing GitHub's review UI, proving code quality, proving
release asset integrity, or preserving the full history of every issue and
comment thread on GitHub

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Export an Approval Receipt (Priority: P1)

As a release manager, I want to generate a receipt for a pull request that
states who approved it, what change set they approved, and what merge-governing
rules applied, so I can prove the release decision without relying on
screenshots or GitHub's current UI state.

**Why this priority**: This is the core product value. If the system cannot
produce a portable approval receipt for one pull request, the registry has no
credible first use case.

**Independent Test**: A repository manager can select an eligible pull request,
generate a receipt, and inspect a complete artifact that states whether the
approval claim is valid, discrepant, or incomplete.

**Acceptance Scenarios**:

1. **Given** a pull request with the required approvals and a mergeable review
   state, **When** the manager generates an approval receipt, **Then** the
   artifact identifies the reviewed change scope, the approving actors, the
   rule context, and the resulting approval outcome.
2. **Given** a pull request whose approval state is affected by stale reviews,
   dismissal, or insufficient visibility, **When** the manager generates an
   approval receipt, **Then** the artifact reports the claim as discrepant or
   incomplete instead of presenting it as valid.

---

### User Story 2 - Verify a Shared Receipt Independently (Priority: P2)

As an auditor or external stakeholder, I want to verify a shared approval
receipt without browsing the original pull request conversation, so I can
independently confirm whether the claimed approval state is supported.

**Why this priority**: Portability is the difference between a helpful internal
dashboard and a sovereign trust artifact. Independent verification is required
for customer trust, audits, and vendor governance.

**Independent Test**: A third party who receives the exported receipt can run a
verification flow and reach a clear result without manual reconstruction from
multiple GitHub pages.

**Acceptance Scenarios**:

1. **Given** a valid approval receipt for a supported pull request, **When** an
   external verifier checks the artifact, **Then** the verifier can determine
   whether the approval claim passes, fails, or is incomplete and can see the
   reason.
2. **Given** an approval receipt that no longer matches the relevant GitHub
   state or that omits required evidence, **When** an external verifier checks
   the artifact, **Then** the verifier sees a discrepancy or incompleteness
   outcome rather than a silent success.

---

### User Story 3 - Surface High-Risk Approval Drift (Priority: P3)

As a repository administrator or security lead, I want the receipt to highlight
high-risk conditions such as review dismissal, last-minute pushes, bypassed
review requirements, or merge outcomes that differ from the reviewed state, so
I can identify governance gaps instead of merely archiving a "green" result.

**Why this priority**: Teams need more than a yes/no badge. The product becomes
materially more valuable when it explains where GitHub's visible approval state
can diverge from a defensible release decision.

**Independent Test**: On pull requests that include known high-risk events, the
receipt clearly classifies the drift and the verifier can see why the approval
claim is weakened or rejected.

**Acceptance Scenarios**:

1. **Given** a pull request where approvals were later dismissed, bypassed, or
   rendered non-equivalent to the merge outcome, **When** a receipt is
   generated or verified, **Then** the artifact explicitly identifies that risk
   instead of collapsing it into a generic failure.

### Edge Cases

- A new commit is pushed after approval, but the repository does not dismiss
  stale reviews.
- The most recent pusher is also an approver, and the repository requires an
  approval from someone other than the last pusher.
- An administrator or authorized actor dismisses a review after the pull
  request appeared approved.
- The pull request is merged using a method that changes the final commit
  identity relative to the reviewed head state.
- Repository rule settings change between review, merge, and verification.
- The verifier has access only to the exported receipt and not to private
  repository contents.
- The integration sees only partial history because of missing permissions,
  missing webhook delivery, or retention limits.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST allow an authorized user to generate a receipt for a
  specific pull request and a clearly identified approval claim.
- **FR-002**: System MUST describe the reviewed change scope associated with the
  receipt, including whether the claim is tied to a pre-merge state, a merged
  outcome, or both.
- **FR-003**: System MUST identify the approval actors, the approval states that
  count toward the claim, and the repository review rules relevant to that
  claim.
- **FR-004**: System MUST classify the approval claim as valid, discrepant, or
  incomplete.
- **FR-005**: System MUST explain any discrepancy or incompleteness in a form
  understandable by a non-author verifier.
- **FR-006**: System MUST allow users to export or share the resulting receipt
  bundle without requiring manual evidence collection from the GitHub UI.

### Trust & Verification Requirements

- **TVR-001**: The feature MUST define which GitHub facts are authoritative for
  the approval claim, including pull request state, review state, merge
  outcome, and review-governing repository rules.
- **TVR-002**: The feature MUST define what the receipt proves and what it does
  not prove, including the distinction between approval governance and code
  correctness.
- **TVR-003**: The feature MUST disclose freshness, visibility, and retention
  limits that could make a receipt incomplete.
- **TVR-004**: The feature MUST specify how later events such as stale-review
  invalidation, review dismissal, force-push-related rewrites, or rule changes
  affect verification outcomes for prior receipts.
- **TVR-005**: The feature MUST support independent verification without
  requiring access to internal operator state.
- **TVR-006**: If the available GitHub evidence is insufficient to support the
  claimed approval state, the feature MUST return an incomplete result rather
  than imply success.

### Key Entities *(include if feature involves data)*

- **Pull Request Approval Claim**: The bounded statement being verified, such as
  "this pull request was approved under the applicable review rules for this
  reviewable state."
- **GitHub Review Fact**: A review decision or related event that contributes
  to, invalidates, or explains the approval claim.
- **Rule Context**: The relevant repository review-governance conditions that
  define what counts as a valid approval.
- **Approval Receipt Bundle**: The portable package containing the claim,
  supporting evidence summary, trust disclaimers, and verification inputs.
- **Verification Outcome**: The result of checking the receipt, including pass,
  discrepancy, or incompleteness with explanatory detail.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A release manager can generate an approval receipt for a
  supported pull request in a single workflow session without collecting manual
  screenshots or spreadsheet evidence from GitHub.
- **SC-002**: An independent verifier can determine whether the receipt's
  approval claim passes, is discrepant, or is incomplete using the exported
  artifact and the documented trust inputs.
- **SC-003**: Replaying unchanged GitHub source material for the same pull
  request yields the same verification outcome.
- **SC-004**: In each supported high-risk case, including stale approvals,
  dismissed reviews, rule mismatches, or merge non-equivalence, the feature
  reports the specific risk instead of a generic success.

## Assumptions

- The organization or repository grants the integration enough access to read
  pull request, review, and repository rule information for the targeted
  repositories.
- The first release targets repositories that use pull request-based review
  governance rather than direct pushes as the primary release control.
- External verifiers accept a bounded governance claim about approval state and
  merge equivalence, not a claim that the merged code is safe or correct.
- Release artifacts, package provenance, and general issue or comment
  notarization remain out of scope for this first feature.
