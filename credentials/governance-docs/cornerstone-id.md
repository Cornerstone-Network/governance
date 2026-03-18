# Cornerstone ID — Schema Documentation

## Table of Contents

- [1. About this Document](#1-about-this-document)
  - [1.1 Version History](#11-version-history)
- [2. Schema Overview](#2-schema-overview)
  - [2.1 Attribute Summary](#21-attribute-summary)
- [3. Schema Definition](#3-schema-definition)
  - [3.1 Attributes](#31-attributes)
- [4. Data Source Requirements](#4-data-source-requirements)
  - [4.1 Identity Verification Sources](#41-identity-verification-sources)
  - [4.2 Evidence Requirements](#42-evidence-requirements)
  - [4.3 Data Currency](#43-data-currency)
- [5. Status Management](#5-status-management)
- [6. Design Rationale](#6-design-rationale)
- [7. Governance](#7-governance)
- [8. Implementation References](#8-implementation-references)

---

## 1. About this Document

This document defines the **Cornerstone ID** schema — a structured data model for the foundational person-level identity credential in the Cornerstone Network. It is intended for governance reviewers, schema implementers, credential issuers, and verifier application developers evaluating whether this schema meets their requirements.

The Cornerstone ID schema captures verified identity attributes for an individual, serving as the base credential that all other Cornerstone credentials reference via `cornerstone_id`. It contains only what it uniquely attests to — verified identity — with no role, property, employment, or derived data.

### 1.1 Version History

| Ver.    | Date         | Notes                                                                 | Author(s)       |
|---------|--------------|-----------------------------------------------------------------------|-----------------|
| **1.0** | 18-Mar-2026  | Rewritten as schema documentation; governance body perspective        | Mathieu Glaude  |
| **0.9** | 26-Feb-2026  | JSON-LD format; replaced `birthdate_dateint` with ISO 8601 `birthdate`; removed `fsa_code`; added `verified_phone` and `cornerstone_user_id` | Mathieu Glaude  |

[↑ Back to top](#table-of-contents)

---

## 2. Schema Overview

The Cornerstone ID schema defines the data structure for the foundational identity credential in the Cornerstone Network. Every participant holds a Cornerstone ID, which is referenced by all other credentials in the ecosystem via the `cornerstone_id` attribute. This schema captures only verified identity — no role, property, employment, or derived data is included. Selective disclosure is handled via BBS+ data integrity cryptosuites at the credential layer.

|                        |                                                                                |
|------------------------|--------------------------------------------------------------------------------|
| **Schema:**            | Cornerstone ID v1.0                                                            |
| **Format:**            | W3C Verifiable Credentials (JSON-LD)                                           |
| **Governance Body:**   | Cornerstone Network                                                            |
| **Schema URI:**        | `https://trustinfrastructure.com/cornerstone/schemas/cornerstone-id.json`      |

### 2.1 Attribute Summary

| **#** | **Name**              | **Attribute**            | **Data Type**     | **Required** |
|-------|-----------------------|--------------------------|-------------------|--------------|
| 001   | Given Names           | `given_names`            | String            | Yes          |
| 002   | Family Name           | `family_name`            | String            | Yes          |
| 003   | Date of Birth         | `birthdate`              | String (date)     | Yes          |
| 004   | Verified Email        | `verified_email`         | String            | Yes          |
| 005   | Verified Phone        | `verified_phone`         | String            | Yes          |
| 006   | Postal Address        | `postal_address`         | JSON object       | No           |
| 007   | Cornerstone User ID   | `cornerstone_user_id`    | String (UUID)     | Yes          |
| 008   | Identity Evidence     | `identity_evidence`      | String / URI      | Yes          |

[↑ Back to top](#table-of-contents)

---

## 3. Schema Definition

### 3.1 Attributes

*Given Names (001)*

<table>
  <tr><th>Attribute</th><td><code>given_names</code></td></tr>
  <tr><th>Description</th><td>Legal given names, may include middle names.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>John Michael</code></td></tr>
</table>

*Family Name (002)*

<table>
  <tr><th>Attribute</th><td><code>family_name</code></td></tr>
  <tr><th>Description</th><td>Legal family name / surname.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>Smith</code></td></tr>
</table>

*Date of Birth (003)*

<table>
  <tr><th>Attribute</th><td><code>birthdate</code></td></tr>
  <tr><th>Description</th><td>Date of birth in ISO 8601 format. Replaces the earlier <code>birthdate_dateint</code> integer format used for AnonCreds ZK proofs.</td></tr>
  <tr><th>Data Type</th><td>String (YYYY-MM-DD)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>1985-06-21</code></td></tr>
</table>

*Verified Email (004)*

<table>
  <tr><th>Attribute</th><td><code>verified_email</code></td></tr>
  <tr><th>Description</th><td>Email address verified via OTP during onboarding.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>john.smith@example.com</code></td></tr>
</table>

*Verified Phone (005)*

<table>
  <tr><th>Attribute</th><td><code>verified_phone</code></td></tr>
  <tr><th>Description</th><td>Phone number verified through Interac or SMS OTP.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>+1-604-555-0123</code></td></tr>
</table>

*Postal Address (006)*

<table>
  <tr><th>Attribute</th><td><code>postal_address</code></td></tr>
  <tr><th>Description</th><td>Residential or mailing address from the identity verification source. Includes <code>street_address</code>, <code>locality</code>, <code>region</code>, <code>postal_code</code>, <code>country</code>.</td></tr>
  <tr><th>Data Type</th><td>JSON object</td></tr>
  <tr><th>Required</th><td>No</td></tr>
  <tr><th>Examples</th><td><code>{"street_address": "123 Main St", "locality": "Vancouver", "region": "BC", "postal_code": "V6B 1A1", "country": "CA"}</code></td></tr>
</table>

*Cornerstone User ID (007)*

<table>
  <tr><th>Attribute</th><td><code>cornerstone_user_id</code></td></tr>
  <tr><th>Description</th><td>Platform-generated opaque identifier. Does NOT encode role information.</td></tr>
  <tr><th>Data Type</th><td>String (UUID)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>550e8400-e29b-41d4-a716-446655440000</code></td></tr>
</table>

*Identity Evidence (008)*

<table>
  <tr><th>Attribute</th><td><code>identity_evidence</code></td></tr>
  <tr><th>Description</th><td>UUID or URI referencing the identity verification evidence record — includes verification source(s), author, and date. Retained five years for regulatory compliance.</td></tr>
  <tr><th>Data Type</th><td>String / URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>urn:uuid:a1b2c3d4-e5f6-7890-abcd-ef1234567890</code></td></tr>
</table>

[↑ Back to top](#table-of-contents)

---

## 4. Data Source Requirements

Credentials issued under this schema are expected to draw from the following authoritative sources.

### 4.1 Identity Verification Sources

Only high-assurance identity verification sources are accepted:

- **Interac Bank Verification Service** — Canada-wide, high-assurance through banking relationships
- **BC Person Credential** — British Columbia provincial digital identity credential

If a holder verifies through multiple sources, the evidence array contains multiple objects documenting each source.

### 4.2 Evidence Requirements

Each evidence object in the credential's `evidence` array must include:

| Field               | Description                                              | Required |
|---------------------|----------------------------------------------------------|----------|
| `type`              | Type of evidence (e.g., "IdentityProofing")              | Yes      |
| `method`            | Verification method (e.g., "InteracBankVerification")    | Yes      |
| `verificationDate`  | ISO 8601 timestamp of verification                       | Yes      |
| `matchFields`       | Array of verified fields (e.g., ["name", "dob"])         | Yes      |
| `recordLocator`     | Reference to stored evidence record (UUID or URI)        | Yes      |
| `verifier`          | Entity performing verification (e.g., "Interac Corp.")   | Yes      |

**Forbidden in evidence:** Assurance level indicators, role or persona indicators, property or employment references, derived predicates.

### 4.3 Data Currency

- A credential reflects the state of identity verification at issuance time.
- Identity changes (name change, contact updates) require revocation and re-issuance.
- Expiration period: 3–5 years after issuance (per operational policy).
- Evidence is retained for five years in compliance with FINTRAC requirements.

[↑ Back to top](#table-of-contents)

---

## 5. Status Management

This schema requires credentials to implement **W3C Bitstring Status List v1.1** for lifecycle management, with separate bitstrings for revocation and suspension.

| Event                        | Action              | Status Handling                           |
|------------------------------|---------------------|-------------------------------------------|
| Identity verified            | Issue credential    | Index set to 0 (valid)                    |
| Account closed               | Revoke              | Revocation bit = 1 (permanent)            |
| Fraud detected               | Revoke              | Revocation bit = 1 (permanent)            |
| Investigation pending         | Suspend             | Suspension bit = 1 (reversible)           |
| Investigation cleared         | Reinstate           | Suspension bit = 0                        |
| Identity change (name)        | Revoke + re-issue   | Old revoked; new credential index = 0     |
| Verification source expired   | Revoke + re-issue   | Old revoked; new credential index = 0     |

**Cascade rule:** The Cornerstone ID is the foundational credential referenced by all others. When revoked, the platform operationally reviews and revokes all dependent credentials: Home Credentials, Professional Credentials, Accreditation Credentials, Portfolio Issuer Credentials, and PAACs.

[↑ Back to top](#table-of-contents)

---

## 6. Design Rationale

**`birthdate` (ISO 8601) replaces `birthdate_dateint`:** The integer format (YYYYMMDD) supported AnonCreds ZK predicate proofs. Since this ecosystem uses W3C JSON-LD format exclusively, selective disclosure is handled via BBS+ data integrity cryptosuites. Standard ISO 8601 dates are appropriate.

**`fsa_code` removed:** Forward Sortation Area existed for AnonCreds geographic predicates. In JSON-LD with BBS+ selective disclosure, verifiers can derive FSA from postal code when needed.

**Email+Phone OTP verification path removed:** Only two high-assurance sources are accepted — Interac Bank Verification and BC Person Credential — simplifying the assurance model.

**Foundational credential principle:** Following the vLEI model, the most mature production credential ecosystems use minimal foundational identity credentials. The Cornerstone ID contains only what it uniquely attests to — verified identity — enabling all other credentials to reference it rather than duplicating identity claims.

**Forbidden data:** This schema explicitly excludes property/homeownership data, employment/professional data, role/persona information, predicates, assurance level indicators, and financial data. These belong in domain-specific credential schemas.

[↑ Back to top](#table-of-contents)

---

## 7. Governance

- **Governance Body:** Cornerstone Network
- **Schema Owner:** Cornerstone Network
- **Review Cycle:** Annual, or upon breaking schema changes
- **Change Process:** Schema updates follow a change-managed governance process to ensure interoperability across all adopting organizations
- **Evidence Retention:** Five years (FINTRAC compliance)

### Schema Versioning

| Level | Change Type | Example |
|-------|-------------|---------|
| Major (v1 → v2) | Breaking changes | Removing attributes, changing data types |
| Minor (v1.0 → v1.1) | Backward-compatible additions | New optional attributes |
| Patch (v1.0.0 → v1.0.1) | Non-breaking fixes | Documentation updates |

[↑ Back to top](#table-of-contents)

---

## 8. Implementation References

| Reference               | Value                                                                          |
|--------------------------|-------------------------------------------------------------------------------|
| **Technical Format**     | W3C Verifiable Credentials Data Model (JSON-LD)                               |
| **Schema URI**           | `https://trustinfrastructure.com/cornerstone/schemas/cornerstone-id.json`     |
| **Context URLs**         | `https://www.w3.org/ns/credentials/v2` <br/> `https://trustinfrastructure.com/cornerstone/contexts/cornerstone-id-v1.0.json` |
| **Schema Registry**      | `https://trustinfrastructure.com/cornerstone/schemas/`                        |
| **Governance Doc**       | `https://openpropertyassociation.ca/credential-governance/cornerstone-id/`    |

### Required Envelope Fields

Credentials issued under this schema must include:
- `issuer` — DID of the issuing organization (must be Cornerstone Network DID)
- `validFrom` and `validUntil` — temporal bounds (3–5 year expiration)
- `credentialSchema` — reference to this schema
- `credentialStatus` — revocation and suspension bitstring entries
- `evidence` — at least one evidence object documenting the identity verification

### Credential Relationships

| Relationship | Credential |
|---|---|
| **Prerequisites** | None (foundational credential) |
| **Referenced by** | Home Credential, Professional Credential, Accreditation Credential, Portfolio Issuer, PAAC |

[↑ Back to top](#table-of-contents)
