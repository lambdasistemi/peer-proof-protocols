# Inclusion, Exclusion, Completeness

These three ideas are related, but they are not the same.

## Inclusion

Inclusion means:

- this fact is in my committed set

Examples:

- this milestone is in my accepted milestones
- this artifact is in my released artifacts
- this credential is in my issued credentials

## Exclusion

Exclusion means:

- this fact is not in my committed set

Examples:

- this credential is not in my revocation set
- this task is not in my disputed tasks
- this asset is not in my custody set

Exclusion becomes meaningful only when the set is well scoped and the queried
key is canonical.

## Completeness

Completeness means:

- this disclosed subset is the whole relevant subset

This is stronger than exclusion. Exclusion answers one negative question.
Completeness answers "there are no other relevant items beyond what I showed."

## Why The Distinction Matters

Protocols often need different combinations:

- inclusion only
- inclusion plus exclusion
- inclusion plus completeness
- all three

The distinction should be made early, because it changes the proof shape and
the protocol cost.
