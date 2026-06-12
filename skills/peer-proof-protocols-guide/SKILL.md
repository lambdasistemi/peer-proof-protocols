---
name: peer-proof-protocols-guide
description: >-
  Guide for working in the lambdasistemi/peer-proof-protocols repository — a
  spec-driven research proposal for peer-only Merkle proof protocols (PPP).
  Load this when a task mentions peer proof protocols, PPP, peer-owned
  Merkle-authenticated state, per-peer roots, proof bundles, inclusion/exclusion
  evidence, certification facts, audit-through vs trust-certifier, proof-request
  language, on-chain root anchoring, the Cardano anchor UTxO (owner key +
  namespace + scope), or candidate protocols such as GS1 EPCIS, in-toto, DCSA,
  Peppol, SWIFT documentary credits, W3C VC Status Lists, OpenID4VC, Hyperledger
  AnonCreds/Aries, or Cardano governance. Also load when editing files under
  docs/, specs/ (001-github-approval-receipts, 002-gs1-epcis-peer-traceability,
  003-in-toto-peer-provenance), mkdocs.yml, or .specify/, when building the docs
  site with `mkdocs build --strict`, or when answering "what is PPP / what does
  this repo do" questions. This repo is documentation only — there is no
  application source code, build binary, or test suite.
---

# Peer Proof Protocols guide

## Repository map

| Path | Purpose |
| --- | --- |
| `docs/index.md` | Landing page: what PPP is, research thesis, suggested reading order |
| `docs/proposal/` | `value-proposition.md`, `research-method.md` — the business/trust argument and candidate scoring method |
| `docs/concepts/` | Proof and trust model: `why-signed-state.md`, `inclusion-exclusion-completeness.md`, `shared-subject.md`, `composition-and-certification.md`, `proof-request-language.md`, `claim-authoring-and-endorsement.md`, `per-author-entitlements.md`, `trust-model.md`, `on-chain-root-anchoring.md`, `anchor-identity-and-claim-history.md`, `fit-rubric.md` |
| `docs/patterns/` | Reusable patterns: `base-facts-and-proof-bundles.md`, `certification-facts.md`, `audit-through-vs-trust-certifier.md` |
| `docs/protocols/` | `index.md` (research pipeline) plus blueprints: `supply-chain-traceability.md`, `software-release-provenance.md`, `credential-status.md`, `endorsement-with-revocation.md`, `milestone-settlement.md` |
| `docs/candidates/` | `index.md` (classified shortlist) plus one `index.md` per candidate (e.g. `gs1-epcis/`, `in-toto/`, `dcsa-bill-of-lading/`, ...) |
| `docs/infrastructure/index.md` | Layered infrastructure map: off-chain peers → publication/anchoring → verifiers |
| `docs/worksheets/protocol-evaluation-sheet.md` | Worksheet to fill before opening a new candidate spec |
| `specs/` | Spec Kit features. `001-github-approval-receipts/` is historical (abandoned oracle/registry direction); `002-gs1-epcis-peer-traceability/` and `003-in-toto-peer-provenance/` are the active peer-only specs. Each has `spec.md`, `plan.md`, `research.md`, `quickstart.md`, `data-model.md`, `checklists/`, `contracts/` (JSON Schema + OpenAPI) |
| `.specify/` | Spec Kit tooling: `memory/constitution.md` (governance), `scripts/bash/`, `templates/` |
| `mkdocs.yml` | Site config and `nav:` tree (every page must be listed here) |
| `.github/workflows/` | `ci.yml` (stub) and `docs.yml` (strict build, PR preview, Pages deploy) |

There is no `src/`, no binary, and no unit-test suite. The deliverable is the
documentation site plus the Spec Kit specs.

## Build, test, run

The only build is the docs site, using the shared MkDocs toolchain from
`paolino/dev-assets` (there is no local flake or package manifest):

```bash
# live-reload preview
nix develop github:paolino/dev-assets?dir=mkdocs --quiet -c mkdocs serve

# strict build, exactly as CI runs it (fails on broken links and warnings)
nix develop github:paolino/dev-assets?dir=mkdocs --quiet -c mkdocs build --strict
```

`mkdocs build --strict` is the gate that matters: it is the closest thing to a
test here. After any docs edit, run it and confirm a clean build. Every page
under `docs/` must be reachable from the `nav:` in `mkdocs.yml`, or the strict
build warns.

## Navigating the code

This repository is documentation, so "navigating the code" means knowing where
each idea is written down:

- **Start at `docs/index.md`** — it states the thesis (signed messages vs signed
  state) and lays out the canonical reading order.
- **The trust/proof vocabulary** lives in `docs/concepts/`. Inclusion, exclusion,
  completeness, freshness, and revocation semantics are in
  `inclusion-exclusion-completeness.md`; who is trusted for what is in
  `trust-model.md`.
- **The end-to-end model** (peer authors state → discloses a scoped proof bundle
  on a pull request → verifier checks against signed or anchored roots → verified
  bundle promoted to a certification fact) is summarized in the README
  Architecture diagram and detailed across `docs/patterns/` and
  `docs/infrastructure/index.md`.
- **The Cardano anchoring shape** (one canonical anchor per owner key + namespace
  + scope, a unique anchor token, owner-controlled monotonic updates, anchor
  UTxOs as smart-contract reference inputs) is in
  `docs/concepts/on-chain-root-anchoring.md` and
  `docs/concepts/anchor-identity-and-claim-history.md`.
- **Concrete targets** are in `docs/protocols/` (blueprints) and
  `docs/candidates/` (analyses). The canonical candidate classification —
  standards-backed, protocol families, internal sketches — is in
  `docs/candidates/index.md`; `docs/protocols/index.md` mirrors it.
- **Hard requirements / rules** for what makes a valid feature are in
  `.specify/memory/constitution.md` (five core principles, certification payload,
  delivery gates).

## Using the proposal

- **To read it**, follow the order in `docs/index.md`: value proposition →
  research method → why signed state → inclusion/exclusion/completeness → protocol
  pipeline + candidates → infrastructure.
- **To add or move a docs page**, create the `.md` file under the right `docs/`
  subdirectory and add it to `nav:` in `mkdocs.yml`; keep cross-page links
  relative and run the strict build.
- **To advance a candidate to a spec**, fill
  `docs/worksheets/protocol-evaluation-sheet.md` first; a candidate advances only
  when it has named peers with disjoint fact authority, one high-value shared
  claim, required positive and negative facts, a proof-bundle shape, a freshness
  requirement, a safe software role, and a documented infrastructure path (see
  `docs/protocols/index.md` "From Candidate To Spec").
- **Spec Kit** drives feature work: constitution → spec → plan → research → tasks
  → implementation, with the helper scripts in `.specify/scripts/bash/`. Active
  specs are `specs/002-*` and `specs/003-*`.

## Answering questions

| User asks | Where the answer lives |
| --- | --- |
| "What is Peer Proof Protocols / what does this repo do?" | `README.md` "What is this" and `docs/index.md` |
| "Why not just use signatures?" | `docs/concepts/why-signed-state.md`, `docs/proposal/value-proposition.md` |
| "How does a verifier check a claim?" | README Architecture diagram, `docs/patterns/base-facts-and-proof-bundles.md`, `docs/concepts/inclusion-exclusion-completeness.md` |
| "What is a certification fact / audit-through vs trust-certifier?" | `docs/concepts/composition-and-certification.md`, `docs/patterns/audit-through-vs-trust-certifier.md`, constitution "Composition & Certification" |
| "How do roots go on-chain (Cardano)?" | `docs/infrastructure/index.md`, `docs/concepts/on-chain-root-anchoring.md`, `docs/concepts/anchor-identity-and-claim-history.md` |
| "Which protocols are being considered?" | `docs/candidates/index.md` and `docs/protocols/index.md` |
| "What's in scope / out of scope?" | `README.md` "Research scope", `docs/index.md` "Proposal Boundary", constitution "Product Scope & Non-Goals" |
| "Is there code I can run?" | No application code yet; only the docs site build. Say so plainly and point to the active specs under `specs/`. |
| "What about the GitHub approval receipts / TypeScript work?" | `specs/001-github-approval-receipts/` — historical only; the repo moved away from that oracle/registry direction |

Present PPP as a research proposal and trust model, not a shipped product. Do not
claim conformance to any external standard, and do not describe features as
implemented unless a spec or page actually documents them.
