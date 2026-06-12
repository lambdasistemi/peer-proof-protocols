# Peer Proof Protocols — Agent Guide

## What this repo is

Peer Proof Protocols (PPP) is a spec-driven research proposal for peer-only
Merkle proof protocols: applications where each peer authors its own
Merkle-authenticated state, exchanges scoped proof bundles (inclusion and
exclusion evidence), and can promote verified bundles into reusable certification
facts. This repository contains documentation, not application code. The outputs
are an MkDocs site under `docs/` and Spec Kit feature artifacts under `specs/`.

There is no `src/`, no build of a binary, and no test suite. An earlier
TypeScript/Fastify oracle direction (`specs/001-github-approval-receipts/`) was
abandoned; do not treat it as the active stack.

## How to work here

The only build is the documentation site, using the shared MkDocs toolchain from
`paolino/dev-assets` (there is no local flake or package manifest):

- Serve locally: `nix develop github:paolino/dev-assets?dir=mkdocs --quiet -c mkdocs serve`
- Build like CI: `nix develop github:paolino/dev-assets?dir=mkdocs --quiet -c mkdocs build --strict`

`mkdocs build --strict` is the real gate: it fails on broken internal links and
warnings. Every page must be reachable from the `nav:` in `mkdocs.yml`. Spec Kit
helper scripts live in `.specify/scripts/bash/`; project governance is in
`.specify/memory/constitution.md`.

## Skills

Activatable procedures live under `skills/`. Load the one whose description
matches your task:

- `skills/peer-proof-protocols-guide/` — how the repository is laid out, how to
  build and verify the docs, where the proof/trust model is documented, and how
  to answer user questions about PPP.
