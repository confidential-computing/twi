---
title: Trusted Workload Identity Reference Architecture
abbrev: TWI Reference Architecture
docname: draft-twi-reference-architecture-latest
category: info
ipr: trust200902
area: Security
workgroup: WIMSE

stand_alone: yes
pi:

  rfcedstyle: yes
  toc: yes
  tocindent: yes
  sortrefs: yes
  symrefs: yes
  strict: yes
  comments: yes
  text-list-symbols: -o*+
  docmapping: yes

author:

-
  name: Mark Novak
  org: JP Morgan
  email: mark.novak@outlook.com

-
  name: Yogesh Deshpande
  org: arm
  email: Yogesh.Deshpande@arm.com

informative:
  RFC9334: rats-arch
  I-D.draft-ietf-wimse-arch: WIMSE

--- abstract
Technological advancement coupled with security concerns demands more and more use cases, that require data-in-use protections.
In addition, regulatory bodies around the world increasingly require data-in-use protection and privacy enhancing technologies.
As these advanced technologies are being deployed, challenge remains how to identify the workloads and put trust behind them?
This draft will address the core issue of managing a trustworthy workload identity meeting Confidential Computing requirements.


--- middle

# Introduction

To be filled

# Conventions and Definitions

{::boilerplate bcp14}

This document uses terms and concepts defined by the WIMSE and RATS architecture. For a complete glossary,
see {{Section 4 of -rats-arch}} & {{-WIMSE}}.


# Glossary
{: #sec-glossary }

This document uses the following terms:

## Workload Identity

## Workload Credential


# TWI Use Cases
Following are some of the use cases demanding trustworthy workload identity.

1. A ML Model Owner (acting as Relying Party) wants to deploy its Workload with a unique identity in a privacy
preserving manner. It needs to ensure that the deployed workload is operating in an environment with certain
security guarantees, such as isolated from cloud hosting environment and can be periodically attested.

2. A Workload operating as a deployed lambda running in an isolated environment.

# Core Requirements
The TWI Core Requirements can be located here. (TO DO Add link here from Mark)

# Alignment or Synergy with WIMSE Architecture
WIMSE defines an architecture for managing workload identity in multi-system environments.

1. The WIMSE Architecture, explains the core concept of Workload Identity in-line with the concept of identity in a TWI world.

2. WIMSE model has a CA/Credential issuer that is responsible with provisioning identity credentials to the workload.
TWI requirement is roughly similar in terms of issuing credential. However the requirements and policies applied for issuing credentials vary as described in the divergence section.

3. WIMSE Architecture, defines Trust Domain, which is the authority that identifies domain within which the identifier is scoped.
TWI Architecture, is aligned with this basic building block.

# Divergence from WIMSE

Trustworthy Workload Identity as detailed in this document has following fundamental divergence from core WIMSE Architecture.

## Workload Confidentiality
The confidentiality of Workload and its Identity from the hosting environment, is the fundamental for TWI.

## Workload Provenance
The policy for issuing Credentials may demand the information about the Provenance of the Workload.

### Integrating Workload Provenance with Credential Issuance


## Workload to Platform binding

## Remote Attestation
Remote Attestation plays a fundamental role in attesting the Workload and ensuring that the Workload is running on a platform that provides required trustwrothy guarantees.

### Integrating Workload Attestation with Credential Issuance
The Credential Issuer would process the Attestation Results and apply its own Appraisal Policy for Attestation results prior to issuing Credentials.



### Remote Attestation of Composite Workloads


# TWI Reference Architecture
{: #sec-ref-architecture }

This sections describes in detail, the Reference Architecture for Trusted Workload Identity system

## Assumptions

## Roles in the TWI Eco-System
The following sub-sections describe the various roles or building blocks that exist in the TWI eco-system.

### Verifier

### Identity Provider

### Credential Issuer

## Reference Architecture




# Security Considerations

<cref>TODO</cref>

# IANA Considerations

## CBOR Tag Registrations


# Acknowledgements


<cref>TODO</cref>
