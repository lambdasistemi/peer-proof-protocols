# Custody Handoff

## Peers

- sender
- receiver
- optional carrier or warehouse

## Base Facts

- sender releases custody
- carrier records transit or exception facts
- receiver acknowledges receipt

## Shared Claim

- `asset X moved from A to B`

## Why It Is Strong

- chain-of-custody is inherently multi-peer
- history matters
- exclusion can matter, such as "asset X is not in my custody set"
- later disputes are common

## Certification Ladder

A receiver or insurer can certify:

- `handoff H completed`

from the underlying sender, carrier, and receiver proofs.
