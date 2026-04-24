# W3C VC Status Lists

W3C Bitstring Status Lists provide a standardized way to publish credential
status such as revocation or suspension.

## Fit

This is an excellent exclusion-heavy candidate. The important claim is often
negative: a credential is not revoked, not suspended, or no longer valid for a
given purpose.

## Likely Software Role

- issuer, holder, or verifier toolkit
- status publisher and verifier
- proof layer for negative claims over issuer-authored state
- bridge between credential bundles and certification facts

## IP / License Signals

Risk level: `Low`

- Bitstring Status List is a W3C Recommendation.
- W3C states that Recommendation-track specifications come with royalty-free
  licensing commitments for implementations under the W3C Patent Policy.
- The remaining care points are document-license and trademark hygiene, not
  whether implementation is allowed.

## Main Non-License Risk

- privacy leaks from status infrastructure
- key management
- wallet and credential-format fragmentation

## Take

This is one of the best candidates when exclusion is central. It is also a clean
place to study how negative claims travel in peer-authored proof bundles.

## Sources

- [Bitstring Status List v1.0](https://www.w3.org/TR/vc-bitstring-status-list/)
- [VC Working Group IPR page](https://www.w3.org/groups/wg/vc/ipr/)
