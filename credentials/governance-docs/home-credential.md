# Home Credential — Schema Documentation

## Table of Contents

- [1. About this Document](#1-about-this-document)
  - [1.1 Version History](#11-version-history)
- [2. Schema Overview](#2-schema-overview)
  - [2.1 Attribute Summary](#21-attribute-summary)
- [3. Schema Definition](#3-schema-definition)
  - [3.1 Attributes](#31-attributes)
- [4. Data Source Requirements](#4-data-source-requirements)
  - [4.1 Identity Binding](#41-identity-binding)
  - [4.2 Property Ownership](#42-property-ownership)
  - [4.3 Data Currency](#43-data-currency)
- [5. Status Management](#5-status-management)
- [6. Design Rationale](#6-design-rationale)
- [7. Governance](#7-governance)
- [8. Implementation References](#8-implementation-references)

---

## 1. About this Document

This document defines the **Home Credential** schema — a structured data model for verifiable credentials that attest to property ownership. It is intended for governance reviewers, schema implementers, credential issuers, and verifier application developers evaluating whether this schema meets their requirements.

The Home Credential schema captures the relationship between a verified individual and a specific property, combining identity binding (via Cornerstone ID) with authoritative land title registry data.

### 1.1 Version History

| Ver.    | Date         | Notes                                                                 | Author(s)       |
|---------|--------------|-----------------------------------------------------------------------|-----------------|
| **1.0** | 18-Mar-2026  | Rewritten as schema documentation; v0.9 attribute structure; governance body perspective | Mathieu Glaude  |
| **0.9** | 26-Feb-2026  | Simplified to 8 attributes; cornerstone_id reference pattern; removed identity embedding | Mathieu Glaude  |
| **0.3** | 29-Sep-2025  | Updated with Landcor property attributes                              | Mathieu Glaude  |
| **0.2** | 3-Sep-2025   | Simplified and reformatted                                            | Mathieu Glaude  |
| **0.1** | 20-Aug-2025  | Initial draft                                                         | Mathieu Glaude  |

[↑ Back to top](#table-of-contents)

---

## 2. Schema Overview

The Home Credential schema defines the data structure for a verifiable credential that binds a verified individual to a specific property based on land title registry records. Each credential issued under this schema contains only the data the credential uniquely attests to — property ownership — while referencing the holder's verified identity via a Cornerstone ID rather than embedding identity attributes directly.

|                        |                                                                                |
|------------------------|--------------------------------------------------------------------------------|
| **Schema:**            | Home Credential v1.0                                                           |
| **Format:**            | W3C Verifiable Credentials (JSON-LD)                                           |
| **Governance Body:**   | Cornerstone Network                                                            |
| **Schema URI:**        | `https://trustinfrastructure.com/cornerstone/schemas/home-credential.json`     |

### 2.1 Attribute Summary

| **#** | **Name**              | **Attribute**         | **Data Type**   | **Required** |
|-------|-----------------------|-----------------------|-----------------|--------------|
| 001   | Cornerstone ID Ref    | `cornerstone_id`      | DID / URI       | Yes          |
| 002   | Parcel Identifier     | `pid`                 | String          | Yes          |
| 003   | Property Address      | `property_address`    | JSON object     | Yes          |
| 004   | Purchase Price        | `purchase_price`      | Number          | Yes          |
| 005   | Purchase Date         | `purchase_date`       | String (date)   | Yes          |
| 006   | Year Built            | `year_built`          | Integer         | Yes          |
| 007   | Neighbourhood         | `neighbourhood`       | String          | Yes          |
| 008   | Homeowner Evidence    | `homeowner_evidence`  | String / URI    | Yes          |

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

*Parcel Identifier (002)*

<table>
  <tr><th>Attribute</th><td><code>pid</code></td></tr>
  <tr><th>Description</th><td>Unique Parcel Identifier (PID) from the provincial land title registry. Essential anchor for linking property data across systems.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>027-263-975</code></td></tr>
</table>

*Property Address (003)*

<table>
  <tr><th>Attribute</th><td><code>property_address</code></td></tr>
  <tr><th>Description</th><td>Standardized postal address including street, locality, region, and postal code.</td></tr>
  <tr><th>Data Type</th><td>JSON object</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>{"street": "1234 W 10th Ave", "locality": "Vancouver", "region": "BC", "postalCode": "V6H 1J9"}</code></td></tr>
</table>

*Purchase Price (004)*

<table>
  <tr><th>Attribute</th><td><code>purchase_price</code></td></tr>
  <tr><th>Description</th><td>Last recorded sale price of the property. Useful for fraud checks, appraisals, and loan risk assessment.</td></tr>
  <tr><th>Data Type</th><td>Number</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>1250000</code></td></tr>
</table>

*Purchase Date (005)*

<table>
  <tr><th>Attribute</th><td><code>purchase_date</code></td></tr>
  <tr><th>Description</th><td>Date of the last property sale transaction in ISO 8601 format.</td></tr>
  <tr><th>Data Type</th><td>String (YYYY-MM-DD)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>2018-05-14</code></td></tr>
</table>

*Year Built (006)*

<table>
  <tr><th>Attribute</th><td><code>year_built</code></td></tr>
  <tr><th>Description</th><td>Year the property was originally constructed. Core input for replacement cost models.</td></tr>
  <tr><th>Data Type</th><td>Integer</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>1987</code></td></tr>
</table>

*Neighbourhood (007)*

<table>
  <tr><th>Attribute</th><td><code>neighbourhood</code></td></tr>
  <tr><th>Description</th><td>Recognized neighbourhood or community name where the property is located.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>Kitsilano</code></td></tr>
</table>

*Homeowner Evidence (008)*

<table>
  <tr><th>Attribute</th><td><code>homeowner_evidence</code></td></tr>
  <tr><th>Description</th><td>URI reference to consolidated verification evidence — identity verification source, land title confirmation, and professional attestation record.</td></tr>
  <tr><th>Data Type</th><td>String / URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>urn:cornerstone:evidence:d4e5f6a7-8901-23bc-def0-456789012345</code></td></tr>
</table>

[↑ Back to top](#table-of-contents)

---

## 4. Data Source Requirements

Credentials issued under this schema are expected to draw from the following authoritative sources.

### 4.1 Identity Binding

The `cornerstone_id` attribute references a separately issued Cornerstone ID credential, which provides high-assurance identity verification (Interac Bank Verification Service or BC Person Credential). This schema does not embed identity attributes directly.

### 4.2 Property Ownership

Property attributes (`pid`, `property_address`, `purchase_price`, `purchase_date`, `year_built`, `neighbourhood`) are sourced from provincial land title registries and property data providers (e.g., Landcor in BC). The PID serves as the canonical anchor linking property data across systems.

### 4.3 Data Currency

- A credential reflects the state of records at the time of issuance.
- Ownership changes, property sales, or identity updates require revocation and re-issuance.
- Evidence is retained for five years in compliance with FINTRAC requirements.

[↑ Back to top](#table-of-contents)

---

## 5. Status Management

This schema requires credentials to implement **W3C Bitstring Status List v1.1** for lifecycle management, with separate bitstrings for revocation and suspension.

| Event                   | Action              | Status Handling                           |
|-------------------------|---------------------|-------------------------------------------|
| Property ownership verified | Issue credential | Index set to 0 (valid)                    |
| Property sold           | Revoke              | Revocation bit = 1 (permanent)            |
| Ownership dispute       | Suspend             | Suspension bit = 1 (reversible)           |
| Dispute resolved        | Reinstate           | Suspension bit = 0                        |
| Evidence expired        | Revoke + re-issue   | Old revoked; new credential index = 0     |

**Cascade rule:** Revoking a Home Credential triggers revocation of all Property Access Authorization Credentials (PAACs) for that specific property only. Other properties held by the same individual are unaffected.

[↑ Back to top](#table-of-contents)

---

## 6. Design Rationale

**Identity reference, not embedding (v0.9):** Earlier versions (v0.1–v0.3) embedded four identity attributes (`given_names`, `family_name`, `birthdate_dateint`, `verified_email`) directly in the schema. These were replaced with a single `cornerstone_id` reference following the atomicity principle — each credential contains only the data it uniquely attests to. This aligns with the vLEI chained credential model.

**Removed `effective_year`:** This BC-specific attribute (renovation-adjusted year) was removed to keep the schema jurisdiction-neutral.

**Removed `jurisdiction`:** The municipality/jurisdiction is captured within the `property_address` JSON object, eliminating redundancy.

**Consolidated evidence fields:** Separate `identity_evidence` and `title_evidence` fields were merged into a single `homeowner_evidence` URI that references a consolidated verification record.

[↑ Back to top](#table-of-contents)

---

## 7. Governance

- **Governance Body:** Cornerstone Network
- **Schema Owner:** Cornerstone Network
- **Review Cycle:** Annual, or upon breaking schema changes
- **Change Process:** Schema updates follow a change-managed governance process to ensure interoperability across all adopting organizations
- **Evidence Retention:** Five years (FINTRAC compliance)

[↑ Back to top](#table-of-contents)

---

## 8. Implementation References

| Reference               | Value                                                                          |
|--------------------------|-------------------------------------------------------------------------------|
| **Technical Format**     | W3C Verifiable Credentials Data Model (JSON-LD)                               |
| **Schema URI**           | `https://trustinfrastructure.com/cornerstone/schemas/home-credential.json`    |
| **Context URLs**         | `https://www.w3.org/ns/credentials/v2` <br/> `https://trustinfrastructure.com/cornerstone/contexts/home-credential-v1.0.json` |
| **Schema Registry**      | `https://trustinfrastructure.com/cornerstone/schemas/`                        |
| **Governance Doc**       | `https://openpropertyassociation.ca/credential-governance/home-credential/`   |

### Required Envelope Fields

Credentials issued under this schema must include:
- `issuer` — DID of the issuing organization
- `validFrom` and `validUntil` — temporal bounds
- `credentialSchema` — reference to this schema
- `credentialStatus` — revocation and suspension bitstring entries

[↑ Back to top](#table-of-contents)
