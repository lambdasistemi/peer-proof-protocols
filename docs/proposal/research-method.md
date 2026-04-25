# Research Method

This repository is a proposal backed by research notes, not a claim that every
listed protocol should become a product.

The purpose of the research is to decide where peer-authored Merkle state adds
material value over ordinary signatures, existing standards, or centralized
operator APIs.

## Research Questions

For each candidate, ask:

- Which peers own distinct facts?
- What shared claim do verifiers need to evaluate?
- Which facts must be included?
- Which blocking facts must be excluded?
- Does historical state, revocation, supersession, or freshness matter?
- Can a verifier check a slice without seeing the whole database?
- Can verified bundles become reusable certification facts?
- What role can we play without becoming an unauthorized network operator?

## Evidence Standard

Candidate notes should distinguish:

- public protocol facts from our interpretation
- implementation-friendly standards from restricted documents
- protocol fit from business, legal, or governance risk
- standards-backed candidates from internal protocol sketches
- peer-side tooling from official conformance or operator roles

When a candidate depends on an external standard, cite primary sources where
possible. When a candidate is only a pattern, say so directly.

## Evaluation Ladder

Use this sequence before opening a full Spec Kit feature:

1. Identify peers, peer-owned facts, and the shared claim.
2. Apply the [Fit Rubric](../concepts/fit-rubric.md).
3. Decide whether signatures alone are enough.
4. Define inclusion, exclusion, completeness, and freshness needs.
5. Sketch the proof bundle and verifier flow.
6. Check the safest software role and IP or governance risks.
7. Decide whether infrastructure needs only signed roots, witnesses, or
   on-chain claim-history anchors.
8. Open a feature spec only if the value proposition survives those gates.

## Decision Outcomes

Each candidate should end in one of four states:

- `reject`: Merkle-authenticated state does not add enough value
- `watch`: promising but blocked by governance, product timing, or missing data
- `spec`: strong enough to become a Spec Kit feature
- `prototype`: ready for schemas, fixtures, verifier rules, and demo UX

## Current Bias

The research deliberately favors peer-side roles:

- producer, holder, issuer, buyer, supplier, carrier, builder, reviewer,
  auditor, verifier, or certifier

It is skeptical of roles that centralize authority:

- registry, official access point, legal title operator, network authority,
  compliance authority, or semantic oracle

That bias is intentional. The project is about sovereign peer state, not about
creating a new trusted middleman.

