# Quickstart: GS1 EPCIS Peer Traceability Proofs

## Goal

Run the first local reference flow that:

1. loads sample supplier and carrier traceability facts,
2. commits each peer's facts into separate authenticated state,
3. exports a traceability proof bundle for one shipment segment,
4. verifies inclusion, scoped exclusion, freshness, and cross-peer consistency,
5. optionally publishes a certification fact and audits through it.

## Prerequisites

- Haskell toolchain suitable for the Merkle Tree Store reference implementation
- WASM-capable build path for browser verifier artifacts
- JSON Schema validator for proof-bundle and certification contracts
- Sample EPCIS-style JSON events or canonical traceability fact fixtures
- Trusted peer keys or local test trust anchors for supplier, carrier, receiver,
  and certifier peers

## Planned repository commands

```bash
nix develop
just fixtures-gs1-epcis
just verify-gs1-epcis-bundle
just certify-gs1-epcis-bundle
just verify-gs1-epcis-browser
```

## Expected local flow

### 1. Generate sample peer facts

```bash
just fixtures-gs1-epcis
```

Expected result:

- supplier fixture includes shipped or packed facts for one subject
- carrier fixture includes custody or transport facts for the same subject
- optional receiver fixture includes accepted or rejected facts
- optional negative fixtures include hold, recall, dispute, or unsupported
  exclusion cases

### 2. Export a traceability proof bundle

The fixture flow creates a bundle shaped by
`contracts/traceability-proof-bundle.schema.json`.

Expected result:

- each disclosed fact names its authoring peer
- each proof references a peer root checkpoint
- bundle coverage states subject, peer scope, time range, and policy version
- unsupported exclusion claims are explicit

### 3. Verify the bundle locally

```bash
just verify-gs1-epcis-bundle
```

Expected result:

- verifier recomputes leaf hashes and peer roots
- verifier checks peer root signatures against local trust anchors
- verifier evaluates inclusion, exclusion, freshness, and cross-peer consistency
- verifier returns `pass`, `fail`, `incomplete`, `stale`, `conflicted`,
  `unsupported`, or `superseded`

### 4. Publish and verify a certification fact

```bash
just certify-gs1-epcis-bundle
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
just verify-gs1-epcis-browser
```

Expected result:

- browser/WASM verifier accepts the same proof bundle
- browser result matches the CLI result for positive, negative, stale, and
  conflicted fixtures

## MVP acceptance check

The quickstart succeeds when:

- at least two independent peer roots are verified for one shared
  traceability segment
- omitted facts are not treated as proven absences
- a valid exclusion proof can prove a scoped negative claim
- unsupported exclusion returns `unsupported` or `incomplete`
- stale roots and conflicting peer facts produce non-pass outcomes
- a certification fact can be verified directly and audited through to inputs
