# Candidate Protocols

This page tracks the current shortlist, the safest likely software role for us,
and the first-pass IP or licensing concerns.

This is a product and protocol planning note, not legal advice.

## How To Read This

- `Merkle fit` is about protocol shape, not market size.
- `Role` is the safest software position for us if we want to avoid becoming the
  official operator of somebody else's network.
- `IP or license risk` is only about protocol texts, software licenses,
  trademarks, and related governance.
- `Main non-license risk` captures the bigger operational constraint when the
  licensing story is not the real problem.

## Safe Default Roles

Across most standards-backed candidates, the safest roles are:

- peer-side toolkit or SDK
- verifier or policy engine
- internal middleware for one participant
- adapter that emits or consumes standard messages without claiming authority

The risky roles are:

- accredited access point, service provider, or registry operator
- official conformance or certification authority
- republisher of standards text, schemas, or branded documentation
- legal title, escrow, or adjudication operator where the surrounding framework
  governs that function

## Shortlist

| Candidate | Merkle fit | Likely role | IP or license risk | Main non-license risk |
| --- | --- | --- | --- | --- |
| [GS1 EPCIS](gs1-epcis/index.md) | Excellent | Peer event publisher, verifier, certification layer | Medium | Adoption and conformance claims |
| [DCSA Track and Trace](dcsa-track-and-trace/index.md) | Strong | Carrier or shipper integration and verifier | Medium | Cross-party trust and conformance expectations |
| [DCSA Bill of Lading](dcsa-bill-of-lading/index.md) | Strong | eBL state engine or verifier for one participant or platform | Medium | Legal interoperability and title-control process |
| [Peppol Post-Award](peppol-post-award/index.md) | Strong | Buyer or supplier middleware and validator | Medium-High | Access point accreditation and network governance |
| [Peppol Pre-Award](peppol-pre-award/index.md) | Strong | Tendering-side packager and verifier | Medium-High | Procurement-law and accreditation requirements |
| [SWIFT Documentary Credits](swift-documentary-credits/index.md) | Medium | Internal bank or corporate tooling | High | Banking regulation and network contracts |
| [in-toto](in-toto/index.md) | Excellent | Attestation producer, verifier, certification layer | Low | Differentiation from adjacent provenance tooling |
| [W3C VC Status Lists](w3c-vc-status-lists/index.md) | Excellent | Issuer, holder, verifier, or status publisher toolkit | Low | Wallet and format fragmentation |
| [OpenID4VC](openid4vc/index.md) | Medium | Wallet, issuer, verifier transport layer | Low | Profile churn and interoperability details |
| [Hyperledger AnonCreds](hyperledger-anoncreds/index.md) | Excellent | Privacy-preserving credential toolkit | Low | Crypto and interoperability complexity |
| [Hyperledger Aries](hyperledger-aries/index.md) | Medium | Agent-to-agent transport and workflow layer | Low | Agent complexity and protocol sprawl |
| [Cardano Governance](cardano-governance/index.md) | Strong | Governance client, wallet, verifier, metadata tooling | Low | Social legitimacy and evolving conventions |
| [Compliance Audit-Signoff](compliance-audit-signoff/index.md) | Strong | Evidence workflow and certification tooling | Low | Regulatory and marketing claims |
| [Dispute Resolution](dispute-resolution/index.md) | Strong | Evidence bundle and arbitrator tooling | Low | Legal-service and evidence-handling obligations |

## Current Read

The strongest low-friction places to explore first are:

- `GS1 EPCIS` for non-software supply-chain events
- `in-toto` for software provenance
- `W3C VC Status Lists` and `Hyperledger AnonCreds` for exclusion-heavy
  credential protocols

The most interesting but highest-friction candidates are:

- `Peppol`
- `DCSA eBL`
- `SWIFT documentary credits`

They are attractive because the business pain is real, but they carry more
governance, accreditation, or legal-process baggage than the protocol alone.
