# Fit Rubric

Use this rubric to decide whether a candidate deserves deeper research. It is
not a market score. It measures whether Merkle-authenticated peer state changes
the trust model enough to justify the complexity.

Score each question `0`, `1`, or `2`.

## Questions

1. `Multi-peer truth`
   - `0`: one authority owns almost all relevant facts
   - `1`: one main authority plus acknowledgements
   - `2`: two or more peers each own necessary facts

2. `Statefulness`
   - `0`: one-shot signed messages are enough
   - `1`: some updates matter
   - `2`: evolving state and old states both matter

3. `Negative claims`
   - `0`: only positive statements matter
   - `1`: some absence or revocation matters
   - `2`: exclusion is central

4. `Selective disclosure`
   - `0`: full disclosure is fine
   - `1`: privacy is useful
   - `2`: verifiers usually need only a slice of each peer's state

5. `Composed outcome`
   - `0`: one proof decides the result
   - `1`: multiple proofs help
   - `2`: the final result is inherently derived from several peer proofs

6. `Dispute or audit pressure`
   - `0`: low stakes, low replay value
   - `1`: moderate
   - `2`: disputes, audits, or delayed settlement are common

7. `Why signatures alone fail`
   - `0`: a signed blob would do
   - `1`: signatures work, but awkwardly
   - `2`: committed state is materially better

8. `Certification ladder`
   - `0`: verified facts are consumed once
   - `1`: some derived claims can be reused
   - `2`: certified derived facts naturally become reusable building blocks

## Interpretation

- `0-6`: bad fit
- `7-10`: maybe
- `11-13`: strong fit
- `14-16`: excellent fit

Before advancing a candidate, pair the score with:

- a one-sentence value proposition
- the safest software role
- the main legal, governance, or operational risk
- the infrastructure layer needed: signed roots, witness feed, metadata
  anchoring, or scripted claim-history anchors

## Shortcut

If a protocol is:

- `multi-peer`
- `stateful`
- `composed`

then it is worth serious investigation.
