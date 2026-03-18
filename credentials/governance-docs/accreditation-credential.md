# Accreditation Credential — Schema Documentation

## Table of Contents

- [1. About this Document](#1-about-this-document)
  - [1.1 Version History](#11-version-history)
- [2. Schema Overview](#2-schema-overview)
  - [2.1 Attribute Summary](#21-attribute-summary)
- [3. Schema Definition](#3-schema-definition)
  - [3.1 Attributes](#31-attributes)
- [4. Data Source Requirements](#4-data-source-requirements)
  - [4.1 Identity Binding](#41-identity-binding)
  - [4.2 Regulator Verification](#42-regulator-verification)
  - [4.3 Data Currency](#43-data-currency)
- [5. Status Management](#5-status-management)
- [6. Design Rationale](#6-design-rationale)
- [7. Governance](#7-governance)
- [8. Implementation References](#8-implementation-references)

---

## 1. About this Document

This document defines the **Accreditation Credential** schema — a structured data model for verifiable credentials that attest to a professional's verified licensing or registration status from a provincial regulator. It is intended for governance reviewers, schema implementers, credential issuers, and verifier application developers evaluating whether this schema meets their requirements.

The Accreditation Credential schema captures the fact of licensure at issuance time — that a verified individual holds a valid professional licence from a recognized regulatory body — with ongoing status changes managed externally via the W3C Bitstring Status List.

### 1.1 Version History

| Ver.    | Date         | Notes                                                                 | Author(s)       |
|---------|--------------|-----------------------------------------------------------------------|-----------------|
| **1.0** | 18-Mar-2026  | Schema documentation; governance body perspective; 7 attributes       | Mathieu Glaude  |
| **0.9** | 26-Feb-2026  | Initial v0.9 published; separated from Professional Credential        | Mathieu Glaude  |

[↑ Back to top](#table-of-contents)

---

## 2. Schema Overview

The Accreditation Credential schema defines the data structure for a verifiable credential that attests to a professional's verified licensing or registration status from a provincial regulator. It captures the fact of licensure at issuance time, with status changes (suspension, revocation, renewal) managed via W3C Bitstring Status List v1.1 rather than mutable credential attributes. This schema is independent of the Professional Credential — a holder may possess one, the other, or both.

|                        |                                                                                            |
|------------------------|--------------------------------------------------------------------------------------------|
| **Schema:**            | Accreditation Credential v1.0                                                              |
| **Format:**            | W3C Verifiable Credentials (JSON-LD)                                                       |
| **Governance Body:**   | Cornerstone Network                                                                        |
| **Schema URI:**        | `https://trustinfrastructure.com/cornerstone/schemas/accreditation-credential.json`        |

### 2.1 Attribute Summary

| **#** | **Name**              | **Attribute**          | **Data Type**     | **Required** |
|-------|-----------------------|------------------------|-------------------|--------------|
| 001   | Cornerstone ID Ref    | `cornerstone_id`       | DID / URI         | Yes          |
| 002   | Profession Category   | `profession_category`  | String            | Yes          |
| 003   | Licence Number        | `licence_number`       | String            | Yes          |
| 004   | Licence Expiry        | `licence_expiry`       | String (date)     | Yes          |
| 005   | Regulator Name        | `regulator_name`       | String            | Yes          |
| 006   | Jurisdiction          | `jurisdiction`         | String            | Yes          |
| 007   | Evidence Reference    | `evidence`             | URI               | Yes          |

[↑ Back to top](#table-of-contents)

---

## 3. Schema Definition

### 3.1 Attributes

*Cornerstone ID Reference (001)*

<table>
  <tr><th>Attribute</th><td><code>cornerstone_id</code></td></tr>
  <tr><th>Description</th><td>DID or URI reference to the holder's verified Cornerstone ID credential. Binds this credential to a verified identity without embedding identity attributes.</td></tr>
  <tr><th>Data Type</th><td>DID / URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>did:web:trustinfrastructure.com:cornerstone:id:a8f3b2c1</code></td></tr>
</table>

*Profession Category (002)*

<table>
  <tr><th>Attribute</th><td><code>profession_category</code></td></tr>
  <tr><th>Description</th><td>Category of the professional's regulated activity. Free-form initially; standardization with a controlled vocabulary is planned.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>Real Estate Broker</code>, <code>Appraiser</code>, <code>Mortgage Broker</code></td></tr>
</table>

*Licence Number (003)*

<table>
  <tr><th>Attribute</th><td><code>licence_number</code></td></tr>
  <tr><th>Description</th><td>Unique licence or registration number issued by the provincial regulator.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><strong>BC (BCFSA):</strong> <code>106104</code><br/><strong>ON (RECO):</strong> <code>R1234567</code><br/><strong>QC (OACIQ):</strong> <code>A9876543</code></td></tr>
</table>

*Licence Expiry (004)*

<table>
  <tr><th>Attribute</th><td><code>licence_expiry</code></td></tr>
  <tr><th>Description</th><td>Expiry date of the professional licence from the regulator record, in ISO 8601 format.</td></tr>
  <tr><th>Data Type</th><td>String (YYYY-MM-DD)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>2027-05-23</code></td></tr>
</table>

*Regulator Name (005)*

<table>
  <tr><th>Attribute</th><td><code>regulator_name</code></td></tr>
  <tr><th>Description</th><td>Name of the provincial regulatory body that governs the licence.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>BCFSA</code> (BC Financial Services Authority)<br/><code>RECO</code> (Real Estate Council of Ontario)<br/><code>OACIQ</code> (Organisme d'autoréglementation du courtage immobilier du Québec)</td></tr>
</table>

*Jurisdiction (006)*

<table>
  <tr><th>Attribute</th><td><code>jurisdiction</code></td></tr>
  <tr><th>Description</th><td>Province or territorial jurisdiction under which the licence is granted.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>BC</code>, <code>ON</code>, <code>QC</code></td></tr>
</table>

*Evidence Reference (007)*

<table>
  <tr><th>Attribute</th><td><code>evidence</code></td></tr>
  <tr><th>Description</th><td>URI reference to the verification evidence record, including regulator registry source, verification author, and date.</td></tr>
  <tr><th>Data Type</th><td>URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>urn:cornerstone:evidence:c0f5d4e3-6789-01bc-def0-234567890def</code></td></tr>
</table>

[↑ Back to top](#table-of-contents)

---

## 4. Data Source Requirements

Credentials issued under this schema are expected to draw from the following authoritative sources.

### 4.1 Identity Binding

The `cornerstone_id` attribute references a separately issued Cornerstone ID credential, which provides high-assurance identity verification. This schema does not embed identity attributes directly.

### 4.2 Regulator Verification

Licensing attributes (`profession_category`, `licence_number`, `licence_expiry`, `regulator_name`, `jurisdiction`) are sourced from provincial real estate regulator registries:

- **BC:** [BCFSA](https://www.bcfsa.ca/) (BC Financial Services Authority)
- **ON:** [RECO](https://www.reco.on.ca/) (Real Estate Council of Ontario)
- **QC:** [OACIQ](https://www.oaciq.com/en/) (Organisme d'autoréglementation du courtage immobilier du Québec)

Verification is performed against the regulator's public registry before credential issuance.

### 4.3 Data Currency

- A credential reflects the state of licensure at the time of issuance.
- Licence changes (suspension, revocation, renewal) are handled via the Bitstring Status List, not by modifying the credential.
- Evidence is retained for five years in compliance with FINTRAC requirements.

[↑ Back to top](#table-of-contents)

---

## 5. Status Management

This schema requires credentials to implement **W3C Bitstring Status List v1.1** for lifecycle management, with **separate bitstrings** for revocation and suspension.

| Event                   | Action              | Status Handling                           |
|-------------------------|---------------------|-------------------------------------------|
| Licence verified        | Issue credential    | Index set to 0 (valid)                    |
| Licence suspended       | Suspend (no re-issue) | Suspension bit = 1 (reversible)         |
| Suspension lifted       | Reinstate (no re-issue) | Suspension bit = 0                     |
| Licence revoked         | Revoke (no re-issue)  | Revocation bit = 1 (permanent)          |
| Licence renewed/upgraded | Revoke old + issue new | Old revoked; new credential index = 0 |

**Key principle:** Mutable status in a signed credential is an anti-pattern. A credential that says `licence_status: suspended` while still cryptographically valid creates confusion. Status is managed externally, and the credential itself only asserts the fact of licensure at issuance time.

[↑ Back to top](#table-of-contents)

---

## 6. Design Rationale

**Separation from Professional Credential (v0.9):** The Accreditation Credential was split from the earlier "Verified Real Estate Professional" schema, which combined organizational affiliation with professional licensing. These are independent facts:
- A person can be employed but unlicensed (Professional Credential only)
- A person can be licensed but not currently affiliated with an organization (Accreditation Credential only)
- A person can be both employed and licensed (both credentials)

This follows reference design patterns from vLEI, OCI, and the BC OrgBook ecosystem.

**Removed `licence_status`:** Earlier versions included a `licence_status` field (Licensed, Suspended, Revoked). This was removed because a credential asserting `status: suspended` is semantically contradictory — the credential itself is still signed and valid, creating confusion. Status changes are handled via the external Bitstring Status List.

**Removed `discipline_notes`:** Discipline history changes over time and becomes stale in a credential. Verifiers who need discipline information should check the regulator's public registry directly, which is always current.

**Removed `regulator_uri`:** The regulator's registry URL is incorporated into the `evidence` reference rather than existing as a separate attribute.

**Identity reference, not embedding:** Four identity attributes were replaced with a single `cornerstone_id` reference, following the atomicity principle.

[↑ Back to top](#table-of-contents)

---

## 7. Governance

- **Governance Body:** Cornerstone Network
- **Schema Owner:** Cornerstone Network
- **Review Cycle:** Annual, or upon breaking schema changes
- **Change Process:** Schema updates follow a change-managed governance process to ensure interoperability across all adopting organizations
- **Evidence Retention:** Five years (FINTRAC compliance)

**Independence:** This schema operates independently of the Professional Credential and Portfolio Issuer Credential schemas. No assumptions should be made about which other credentials a holder possesses.

[↑ Back to top](#table-of-contents)

---

## 8. Implementation References

| Reference               | Value                                                                                        |
|--------------------------|----------------------------------------------------------------------------------------------|
| **Technical Format**     | W3C Verifiable Credentials Data Model (JSON-LD)                                              |
| **Schema URI**           | `https://trustinfrastructure.com/cornerstone/schemas/accreditation-credential.json`          |
| **Context URLs**         | `https://www.w3.org/ns/credentials/v2` <br/> `https://trustinfrastructure.com/cornerstone/contexts/accreditation-credential-v1.0.json` |
| **Schema Registry**      | `https://trustinfrastructure.com/cornerstone/schemas/`                                       |
| **Governance Doc**       | `https://openpropertyassociation.ca/credential-governance/accreditation-credential/`         |

### Required Envelope Fields

Credentials issued under this schema must include:
- `issuer` — DID of the issuing organization
- `validFrom` and `validUntil` — temporal bounds
- `credentialSchema` — reference to this schema
- `credentialStatus` — both revocation and suspension bitstring entries

[↑ Back to top](#table-of-contents)
