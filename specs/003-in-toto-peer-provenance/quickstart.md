# Quickstart: in-toto Peer Provenance Proofs

## Goal

Run the first local reference flow that:

1. loads sample in-toto layout, link, statement, predicate, envelope, or bundle
   evidence,
2. maps evidence into canonical peer facts,
3. commits each peer's facts into separate authenticated state,
4. exports a provenance proof bundle for one artifact digest,
5. verifies inclusion, scoped exclusion, freshness, policy semantics, and
   cross-peer consistency,
6. optionally publishes a certification fact and audits through it.

## Prerequisites

- Haskell toolchain suitable for the Merkle Tree Store reference implementation
- WASM-capable build path for browser verifier artifacts
- JSON Schema validator for proof-bundle and certification contracts
- Sample in-toto metadata fixtures
- Trusted peer keys or local test trust anchors for project owner, builder,
  scanner, publisher, and certifier peers

## Planned repository commands

```bash
nix develop
just fixtures-in-toto
just verify-in-toto-bundle
just certify-in-toto-bundle
just verify-in-toto-browser
```

## Expected local flow

### 1. Generate sample peer facts

```bash
just fixtures-in-toto
```

Expected result:

- project-owner fixture includes layout or policy facts
- builder fixture includes build provenance facts for one artifact digest
- scanner fixture includes test, SBOM, vulnerability, or runtime trace facts
- publisher fixture includes release or package publication facts
- optional negative fixtures include revocation, failed test, release hold, or
  unsupported exclusion cases

### 2. Export a provenance proof bundle

The fixture flow creates a bundle shaped by
`contracts/provenance-proof-bundle.schema.json`.

Expected result:

- each disclosed fact names its authoring peer
- each proof references a peer root checkpoint
- in-toto evidence references preserve statement, predicate, envelope, layout,
  link, or bundle context
- bundle coverage states artifact digest, peer scope, predicate families,
  policy version, and limits
- unsupported exclusion claims are explicit

### 3. Verify the bundle locally

```bash
just verify-in-toto-bundle
```

Expected result:

- verifier recomputes leaf hashes and peer roots
- verifier checks peer root signatures against local trust anchors
- verifier validates relevant in-toto evidence or references
- verifier evaluates inclusion, exclusion, freshness, policy, and cross-peer
  consistency
- verifier returns `pass`, `fail`, `incomplete`, `stale`, `conflicted`,
  `unsupported`, or `superseded`

### 4. Publish and verify a certification fact

```bash
just certify-in-toto-bundle
```

Expected result:

- certifier verifies the input bundle
- certifier publishes a certification fact shaped by
  `contracts/certification-fact.schema.json`
- certification pins input bundle digest, input roots, policy, result,
  certifier identity, and epoch
- downstream verifier can run `trust certifier` or `audit through`

### 5. Check browser verifier parity

```bash
just verify-in-toto-browser
```

Expected result:

- browser/WASM verifier accepts the same proof bundle
- browser result matches the CLI result for positive, negative, stale,
  unsupported, and conflicted fixtures

## MVP acceptance check

The quickstart succeeds when:

- at least two independent peer roots are verified for one artifact digest
- omitted attestations are not treated as proven absences
- a valid exclusion proof can prove a scoped negative claim
- unsupported exclusion returns `unsupported` or `incomplete`
- stale roots and conflicting peer facts produce non-pass outcomes
- a certification fact can be verified directly and audited through to inputs
