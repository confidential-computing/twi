# Trustworthy Workload Identity (TWI) — Definitions, Entities and Relationships
Version 1.0

_Trust_ is a decision, _trustworthiness_ is an externally verifiable/attributable characteristic on which to base trust decisions.

## Supporting Definitions
- **Workload** as used in this document restricts the definition of the same term by WIMSE **\[1]** — “a running instance of software executing for a specific purpose” — to just that part of the code and configuration of the (WIMSE-defined) workload that is subject to Remote Attestation.
- **Workload Identity Document** is a verifiable statement that binds the Workload to a cryptographic identity, including, at a minimum, a signed public key and, optionally, any number of additional claims.
- **Workload Identifier** is a stable construct, represented by a Workload Identity Document, around which Relying Parties can form long-lived Workload authorization policies.
- **Workload Identity** is the alias of the Workload as perceived by the Relying Party based on which Workload Identifier is presented to it by the Workload Instance.
- **Workload Credential** is an ephemeral representation of a Workload Identifier, that can be short- or long-lived and which is used to represent and prove Workload Identity to a Relying Party (WIMSE calls this "identity credentials").
- **Workload Provenance** is a unique linkage between a Workload Credential and the trusted entities (such as a vendor, developer, or credential issuer) responsible for the creation and/or attestation of the corresponding Workload.

## Trustworthy Workload Identity Definition
A **Workload Identity** is said to be **Trustworthy** _iff_ the following three properties hold true:

1. **Isolation**: The Workload is confidentiality- and integrity-isolated from its hosting environment as well as peer workloads throughout the lifetime of the Workload instance
2. **Credential Binding**: Any Workload Credential representing a Workload Identity is bound to that Workload instance
3. **Provenance Availability**: A Workload Credential can be traced back to the Workload’s Provenance and the credential issuer

Practical (deployable, performant, compatible, manageable) solutions in the TWI space will have to satisfy several additional requirements, captured in **\[2]**.

## The Deeper Meaning of the TWI-Related Definitions
Below is some additional context and clarifications for the definitions just given, starting with a few notes on the supporting definitions.

### Workload: Clarifications
- Workloads can nest (be composed of smaller individual sub-Workloads). These sub-Workloads can have individual Identities used for, e.g., intra-Workload communications. However, any Workload Identity presented externally (as opposed to peer sub-Workloads) is treated as an Identity of the containing Workload as a whole.

### Workload Identity Document: Clarifications
- In the context of Confidential Computing, at least some of the "additional claims" referred to by the defintion above will relate to the Workload's code, configuration, and execution environment as validated through Remote Attestation of the Workload Instance and the hardware it is executing on.

### Workload Identifier: Clarifications
- A given Workload can have multiple Workload Identifiers, which may or may not be correlatable. The human analog is a person having a Drivers License, a Passport and a Social Security Number, all of which are unique but can only belong to one individual and are thus correlatable by design. Alternatively a human can have multiple email addresses or decentralized identities that are designed to avoid correlation.
- The concept of Workload Identifier is best thought of from the standpoint of the Relying Party. For instance, what constitutes a “Payroll Application” (workload) for purposes of authorizing its access to the “Payroll Database” (relying party) will change as the Payroll Application is upgraded from version N to version N+1, yet the Relying Party policy will not change as the upgrade takes place. The change to Payroll Application’s Reference Values will be 100% contained by the process of Workload Credential issuance. In some cases, the Workload may change so significantly that old Identifiers no longer apply and new ones need to be established.
- Note that Workload Identifier is __represented__ by the Workload Identity Document: it is not __embodied__ by it. The mechanism by which the Workload Identifier is presented to the Relying Party is the Workload Credential.

### Workload Identity: Clarifications
- A given Workload can have multiple Workload Identities. Most typically this manifests when a given Workload may present different Workload Identities for purposes of inbound and outbound authentication, where different Relying Parties expect different Workload Identifiers.

### Trustworthy Workload Identity: Clarifications

1. **Isolation** Property

The following  aspects contribute to a Workload being properly isolated from its hosting environment:
- The Workload needs to execute inside a TEE (or a set of mutually trusting TEEs) on properly designed and trustworthy silicon.
- The Workload needs to be properly governed per the Workload Governance Pattern published by the GRC SIG **\[3]**. This includes aspects of secure design and deployment, proper configuration, and much else.
- Both code and data are subject to the integrity requirement. Whether the code and data require confidentiality is scenario specific: e.g., for sealed-glass proofs, neither code nor data need to be confidential, but for video games both code and data must have confidentiality protection. At a minimum, the confidentiality requirement MUST extend to the mechanism (typically, a cryptographic key) used to prove the Workload Credential to a relying party.

2. **Credential Binding** Property
- A Workload that uses bearer tokens to communicate its Workload Credentials cannot be said to have a Trustworthy Identity, since the credential isn’t bound to the Workload instance. While this may be required for interoperability and may necessitate additional compensating controls to mitigate the threats, such mitigations are outside the scope of the TWI SIG.
- The binding to the credential issuer is fairly straightforward: the issuer signs the issued credential with their private key.
- 
3. **Workload Provenance** Property
- Provenance is a required property of TWI because the first two requirements, while _necessary_, are not _sufficient_ to establish trustworthiness. In layman terms, one may be sure that Bob can keep secrets (confidentiality) and can be reasonably assumed to only execute a given piece of code as well as not tamper with his data (integrity). From those facts alone one cannot tell anything about Bob’s trustworthiness (_reputation_) __– that can be done by “asking around” and that’s where provenance comes in handy.
- Provenance provides linkage between the Workload Credential and some auditable metadata – such as issuer policies, issuance records, or unique identifiers – that ties the Credential to both the Workload instance and the issuer’s trust domain.
- “Lineage” vs. “Provenance”: we use “provenance” here rather than “lineage” deliberately. Provenance may mean a way to identify what source code, build system, etc. were used to generate the confidential computing payload behind the workload. It can also mean a way of establishing that the workload came from a trusted vendor with no way of establishing lineage further than that. Example: an X.509-SVID can contain a serial number. That serial number can be used to associate a credential instance with a set of recorded policy decisions and other data used in issuing this credential. This is just for illustration. A more prescriptive guidance will be an output of the SIG. From ChatGPT:
  - **Lineage** is about **tracing back relationships or transformations** (data or family connections).
  - **Provenance** is about **documenting the origin and history of something** (data records, artifacts, or artworks).
- Provenance, once established, can be used in many ways, chief among them auditing and assisting in real-time authorization decisions. These mechanisms will be specified in the Reference Architecture.

## Relationships between Entities

TODO: Replace with a diagram

Workload - 1:many - Workload Identity ("has-a" relationship)
Workload Identity - 1:many - Workload Identifier ("has-a" relationship)
Workload Credential - many:1 - Workload Identifier ("has-a" relationship)
Workload Credential - 1:1 - Identity Document ("is-a" relationship)

## References
1. Workload Identity in Multi-System Environments IETF Working Group: <https://datatracker.ietf.org/wg/wimse/documents/>
2. Trustworthy Workload Identity Scenarios and Requirements: <https://github.com/confidential-computing/twi/blob/main/TWI_Requirements.md>
3. Confidential Workload Governance pattern: <https://github.com/confidential-computing/governance/blob/main/SIGs/GRC/publications/Confidential_Workload_Governance.md>
