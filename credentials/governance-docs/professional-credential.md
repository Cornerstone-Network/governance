# Professional Credential — Schema Documentation

## Table of Contents

- [1. About this Document](#1-about-this-document)
  - [1.1 Version History](#11-version-history)
- [2. Schema Overview](#2-schema-overview)
  - [2.1 Attribute Summary](#21-attribute-summary)
- [3. Schema Definition](#3-schema-definition)
  - [3.1 Attributes](#31-attributes)
- [4. Data Source Requirements](#4-data-source-requirements)
  - [4.1 Identity Binding](#41-identity-binding)
  - [4.2 Employer/Organization Verification](#42-employerorganization-verification)
  - [4.3 Data Currency](#43-data-currency)
- [5. Status Management](#5-status-management)
- [6. Design Rationale](#6-design-rationale)
- [7. Governance](#7-governance)
- [8. Implementation References](#8-implementation-references)

---

## 1. About this Document

This document defines the **Professional Credential** schema — a structured data model for verifiable credentials that attest to a professional's organizational affiliation. It is intended for governance reviewers, schema implementers, credential issuers, and verifier application developers evaluating whether this schema meets their requirements.

The Professional Credential schema captures the relationship between a verified individual and their employer or organization, confirming active affiliation without embedding identity or licensing details.

### 1.1 Version History

| Ver.    | Date         | Notes                                                                 | Author(s)       |
|---------|--------------|-----------------------------------------------------------------------|-----------------|
| **1.0** | 18-Mar-2026  | Rewritten as schema documentation; v0.9 attribute structure; governance body perspective | Mathieu Glaude  |
| **0.9** | 26-Feb-2026  | Simplified to 6 attributes; separated licensing into Accreditation Credential; cornerstone_id reference | Mathieu Glaude  |
| **0.2** | 12-Nov-2025  | Restructured into Real Estate Professional with 3 attribute categories | Mathieu Glaude  |
| **0.1** | 3-Sep-2025   | Initial draft                                                         | Mathieu Glaude  |

[↑ Back to top](#table-of-contents)

---

## 2. Schema Overview

The Professional Credential schema defines the data structure for a verifiable credential that attests to a professional's organizational affiliation. It captures the fact that a verified individual is actively affiliated with an employer or organization in a specific role, without embedding identity attributes or professional licensing details — those are handled by the Cornerstone ID and Accreditation Credential schemas respectively.

|                        |                                                                                         |
|------------------------|-----------------------------------------------------------------------------------------|
| **Schema:**            | Professional Credential v1.0                                                            |
| **Format:**            | W3C Verifiable Credentials (JSON-LD)                                                    |
| **Governance Body:**   | Cornerstone Network                                                                     |
| **Schema URI:**        | `https://trustinfrastructure.com/cornerstone/schemas/professional-credential.json`      |

### 2.1 Attribute Summary

| **#** | **Name**            | **Attribute**        | **Data Type**       | **Required** |
|-------|---------------------|----------------------|---------------------|--------------|
| 001   | Cornerstone ID Ref  | `cornerstone_id`     | DID / URI           | Yes          |
| 002   | Employer Name       | `employer_name`      | String              | Yes          |
| 003   | Role / Title        | `role_title`         | String              | Yes          |
| 004   | Affiliation Type    | `affiliation_type`   | Enum                | Yes          |
| 005   | Start Date          | `start_date`         | String (date)       | Yes          |
| 006   | Team / Office       | `team_office`        | String              | No           |

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

*Employer Name (002)*

<table>
  <tr><th>Attribute</th><td><code>employer_name</code></td></tr>
  <tr><th>Description</th><td>Name of the organization with which the professional is affiliated.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>Sutton Group Realty Inc.</code>, <code>Royal LePage</code></td></tr>
</table>

*Role / Title (003)*

<table>
  <tr><th>Attribute</th><td><code>role_title</code></td></tr>
  <tr><th>Description</th><td>The professional's role or title within the organization.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>Real Estate Professional</code>, <code>Managing Broker</code>, <code>Property Manager</code></td></tr>
</table>

*Affiliation Type (004)*

<table>
  <tr><th>Attribute</th><td><code>affiliation_type</code></td></tr>
  <tr><th>Description</th><td>Nature of the professional's relationship with the organization.</td></tr>
  <tr><th>Data Type</th><td>Enum</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Values</th><td><code>employee</code>, <code>contractor</code>, <code>delegate</code></td></tr>
</table>

*Start Date (005)*

<table>
  <tr><th>Attribute</th><td><code>start_date</code></td></tr>
  <tr><th>Description</th><td>Date the professional's affiliation with the organization began, in ISO 8601 format.</td></tr>
  <tr><th>Data Type</th><td>String (YYYY-MM-DD)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>2020-03-15</code></td></tr>
</table>

[↑ Back to top](#table-of-contents)

---

## 4. Data Source Requirements

Credentials issued under this schema are expected to draw from the following authoritative sources.

### 4.1 Identity Binding

The `cornerstone_id` attribute references a separately issued Cornerstone ID credential, which provides high-assurance identity verification. This schema does not embed identity attributes directly.

### 4.2 Employer/Organization Verification

Employer data (`employer_name`, `role_title`, `affiliation_type`, `start_date`, `team_office`) is provided by the employer's system of record (e.g., HR system, brokerage management platform). The employer confirms the professional's active affiliation out-of-band before a credential is issued.

### 4.3 Data Currency

- A credential reflects the state of affiliation at the time of issuance.
- Employment termination, role changes, or organizational changes require credential revocation.
- A `status` attribute was intentionally omitted — a credential attesting `status: terminated` is semantically contradictory. Status changes are handled via the Bitstring Status List (see Section 5).

[↑ Back to top](#table-of-contents)

---

## 5. Status Management

This schema requires credentials to implement **W3C Bitstring Status List v1.1** for lifecycle management, with separate bitstrings for revocation and suspension.

| Event                    | Action              | Status Handling                           |
|--------------------------|---------------------|-------------------------------------------|
| Affiliation confirmed    | Issue credential    | Index set to 0 (valid)                    |
| Employment terminated    | Revoke              | Revocation bit = 1 (permanent)            |
| Role change              | Revoke + re-issue   | Old revoked; new credential index = 0     |
| Leave of absence         | Suspend             | Suspension bit = 1 (reversible)           |
| Return from leave        | Reinstate           | Suspension bit = 0                        |
| Fraud detected           | Revoke              | Revocation bit = 1 (permanent)            |

[↑ Back to top](#table-of-contents)

---

## 6. Design Rationale

**Separation from Accreditation (v0.9):** Earlier versions (v0.1–v0.2) combined organizational affiliation with professional licensing in a single 24-attribute "Verified Real Estate Professional" schema. This was split into two independent schemas:
- **Professional Credential** — organizational affiliation (this schema)
- **Accreditation Credential** — professional licensing from a regulator

A holder may possess only a Professional Credential (employed but unlicensed), only an Accreditation Credential (licensed but not currently affiliated), or both. This separation follows the principle that each credential attests to a single verifiable fact.

**Identity reference, not embedding:** Four identity attributes were replaced with a single `cornerstone_id` reference, following the atomicity principle aligned with the vLEI chained credential model.

**No `status` field:** A credential asserting `status: terminated` is semantically contradictory. Status changes are managed externally via the W3C Bitstring Status List without credential re-issuance.

**Simplified attribute set:** Attributes like `specialties`, `service_areas`, `join_year`, and `employer_evidence` were removed. The schema focuses on what can be authoritatively verified by the employer — affiliation, role, and start date. Profile-level details belong in application-layer data, not verifiable credentials.

[↑ Back to top](#table-of-contents)

---

## 7. Governance

- **Governance Body:** Cornerstone Network
- **Schema Owner:** Cornerstone Network
- **Review Cycle:** Annual, or upon breaking schema changes
- **Change Process:** Schema updates follow a change-managed governance process to ensure interoperability across all adopting organizations

[↑ Back to top](#table-of-contents)

---

## 8. Implementation References

| Reference               | Value                                                                                   |
|--------------------------|----------------------------------------------------------------------------------------|
| **Technical Format**     | W3C Verifiable Credentials Data Model (JSON-LD)                                        |
| **Schema URI**           | `https://trustinfrastructure.com/cornerstone/schemas/professional-credential.json`     |
| **Context URLs**         | `https://www.w3.org/ns/credentials/v2` <br/> `https://trustinfrastructure.com/cornerstone/contexts/professional-credential-v1.0.json` |
| **Schema Registry**      | `https://trustinfrastructure.com/cornerstone/schemas/`                                 |
| **Governance Doc**       | `https://openpropertyassociation.ca/credential-governance/professional-credential/`    |

### Required Envelope Fields

Credentials issued under this schema must include:
- `issuer` — DID of the issuing organization
- `validFrom` and `validUntil` — temporal bounds
- `credentialSchema` — reference to this schema
- `credentialStatus` — revocation and suspension bitstring entries

[↑ Back to top](#table-of-contents)
