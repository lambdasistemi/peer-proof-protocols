# Why Signed State

The core distinction is:

- a signed message proves "I said this"
- a signed state proves "this fact was part of my committed state"

That difference matters when a protocol needs more than one-off authorship.

## What Signed State Adds

Signed state gives you:

- `membership`: this fact was in my set
- `absence`: this fact was not in my set
- `history`: this was true under root `R` at a given time or version
- `selective disclosure`: I can reveal one slice without revealing everything
- `amortization`: one root authenticates many facts
- `composition`: verified facts can be used to derive higher-order facts

## When It Matters

Signed state is worth the cost when:

- more than one peer contributes facts to the outcome
- facts evolve over time
- old states still matter
- verifiers usually need only a subset of the total state
- disputes or audits are likely

## When It Does Not

Signed state is usually not worth it when:

- one signed document is enough
- one authority owns almost all relevant truth
- full disclosure is acceptable
- the protocol has no real historical or dispute pressure

## Practical Test

A protocol is a strong candidate when it is:

- `multi-peer`
- `stateful`
- `partial`

If one of those is missing, plain signatures may be enough.
