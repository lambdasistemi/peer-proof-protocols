# Quickstart: GitHub Approval Receipts

## Goal

Run the first local flow that:

1. fetches GitHub pull request approval evidence,
2. issues a signed approval receipt bundle, and
3. verifies that bundle independently.

## Prerequisites

- Node.js 22 LTS
- pnpm
- A GitHub repository where the operator can read pull requests, reviews, and
  applicable repository rules
- Either:
  - a GitHub App installation configured for the target repository, or
  - a fine-grained token suitable for local development

## Planned repository commands

```bash
pnpm install
pnpm dev:api
pnpm receipt:issue --owner ORG --repo REPO --pull 123 > receipt.json
pnpm receipt:verify --file receipt.json
pnpm dev:browser
```

## Expected local flow

### 1. Start the registry API

```bash
pnpm dev:api
```

This starts the issuance API and local SQLite-backed registry.

### 2. Issue a receipt

```bash
pnpm receipt:issue --owner ORG --repo REPO --pull 123 > receipt.json
```

Expected result:

- `receipt.json` contains a classification of `valid`, `discrepant`, or
  `incomplete`
- the file includes a receipt root, the canonical facts needed for verification,
  and a registry signature

### 3. Verify the receipt in CLI

```bash
pnpm receipt:verify --file receipt.json
```

Expected result:

- the CLI recomputes the evidence root
- the CLI verifies the registry signature
- the CLI prints the classification and reason codes

### 4. Verify the same receipt in browser

```bash
pnpm dev:browser
```

Open the local verifier page, upload `receipt.json`, and confirm the browser
shows the same verification outcome as the CLI.

## MVP acceptance check

The quickstart succeeds when:

- the same receipt verifies identically in CLI and browser
- stale, dismissed, or non-equivalent merges do not produce false `valid`
  outcomes
- partial rule visibility yields `incomplete` instead of silent success
