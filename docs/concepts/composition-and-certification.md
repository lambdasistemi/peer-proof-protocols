# Composition and Certification

Composition here is mostly semantic, not magical proof compression.

It means:

1. verify a bundle of lower-level facts and proofs
2. apply a rule or policy to them
3. publish the derived result as a new fact

So verified bundles can become certified facts.

## Base Pattern

The basic ladder is:

```text
base facts -> verified proof bundle -> derived claim -> certification fact
```

Example:

- vendor proves `delivered(milestone M, artifact A)`
- QA proves `passed(milestone M, suite S)`
- client proves `accepted(milestone M)`
- client excludes `disputed(milestone M)`

A certifier can verify that bundle and publish:

- `milestone M payable under policy P`

That new certification can then be reused later.

## Why This Matters

It gives:

- modular protocols
- reusable verified outcomes
- institutional memory
- higher-order claims without central truth ownership

## What A Certification Fact Must Pin

A certification fact should include at least:

- subject identifier
- rule or policy identifier
- rule or policy version
- result or classification
- input bundle digest
- input roots or checkpoints
- certifier identity
- certification time or epoch

Without pinned inputs, a certification is just a vague statement.

## Downstream Verification Modes

There are three useful modes:

- `trust certifier`: verify only the certification fact and the certifier root
- `audit through`: traverse from the certification to the underlying inputs and
  verify the base proofs too
- `mixed`: allow both, but disclose that the trust surface is different

The mode must be explicit in the protocol.
