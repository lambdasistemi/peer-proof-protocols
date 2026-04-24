# Certification Facts

A certification fact is a derived fact published after verifying an input
bundle.

## Purpose

Certification facts let peers promote verified outcomes into reusable building
blocks.

Examples:

- `qa_certifies release R passed suite S`
- `client_certifies milestone M accepted`
- `auditor_certifies control set C satisfies policy P`

## Minimum Payload

A certification fact should pin:

- subject identifier
- policy or rule identifier
- policy or rule version
- result
- input bundle digest
- input roots or checkpoints
- certifier identity
- certification epoch

## Why This Is Better Than A Plain Statement

A plain statement says:

- "I certify X"

A certification fact says:

- "I certify X under rule `R` from verified input bundle `B`"

That difference is what makes later audit possible.
