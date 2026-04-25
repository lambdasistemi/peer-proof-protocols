# Value Proposition

PPP targets trust gaps in multi-party applications where the important question
is not only "who signed this message?" but "what state did this peer commit to,
what did it exclude, and can that be checked later by someone else?"

## Pain Point

Many useful applications have the same shape:

- one party publishes a claim
- another party accepts, disputes, scores, or certifies it
- a third party later needs evidence
- not all facts can be public
- the absence of a blocking fact matters
- timestamps, revocations, and supersession matter later

Plain signatures prove authorship of individual messages. They do not give a
compact way to prove set membership, absence, current support, or scoped
completeness across an evolving peer-owned state.

## Proposed Benefit

PPP should let each peer say:

```text
this is my committed state root
this is the fact slice I disclose
this is the proof that the slice is included or excluded
this is the policy that makes the disclosed slice enough
```

That gives verifiers:

- local verification without a privileged registry
- selective disclosure instead of full database replication
- negative evidence for revocation, disputes, holds, or supersession
- reusable certifications that can be trusted directly or audited through
- a path to public timestamping when roots are anchored on-chain

## User Experience Target

The target experience is deliberately simple:

```text
requester sends a proof request through any transport
holder opens or imports the request in a browser, wallet, CLI, or agent
holder returns a proof bundle if the claim is still supportable
verifier checks the bundle against trusted roots or anchored roots
```

New claims are still pushed or published by their authors. Proof requests are
for known claims: "prove you still stand behind this" rather than "tell me
everything you know."

## Why It Is Worth Researching

PPP is attractive when it can reduce one of these costs:

- trust cost: fewer parties need to trust a platform operator's hidden state
- disclosure cost: peers reveal only the relevant slice
- audit cost: later verifiers can replay the evidence
- dispute cost: timestamps, revocations, and exclusions are explicit
- integration cost: existing public protocols can carry an added proof layer

The value is highest when several of those costs appear together.

## Where The Product Could Live

The safest product surface is peer-side infrastructure:

- SDKs for canonical facts, roots, and proof bundles
- browser or CLI verifiers
- request and response envelopes for proof exchange
- adapters for existing standards and APIs
- Cardano anchoring components for public root history

The risky surface is becoming the registry, certifier, access point, legal
operator, or oracle for someone else's network. PPP should strengthen peer
sovereignty rather than replace one trusted operator with another.

## Success Criteria

A PPP candidate is successful when an external verifier can:

- identify the peer whose state is relevant
- obtain the disclosed facts and proofs
- verify inclusion and required exclusions
- resolve freshness against signed or anchored roots
- evaluate a named policy without hidden operator state
- understand what is still trusted, unsupported, or incomplete

