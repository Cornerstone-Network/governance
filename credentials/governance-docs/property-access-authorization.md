# Property Access Authorization — Schema Documentation

## Table of Contents

- [1. About this Document](#1-about-this-document)
  - [1.1 Version History](#11-version-history)
- [2. Schema Overview](#2-schema-overview)
  - [2.1 Attribute Summary](#21-attribute-summary)
- [3. Schema Definition](#3-schema-definition)
  - [3.1 Attributes](#31-attributes)
  - [3.2 Enumerated Values](#32-enumerated-values)
- [4. Data Source Requirements](#4-data-source-requirements)
  - [4.1 Prerequisite Credentials](#41-prerequisite-credentials)
  - [4.2 Authorization Source](#42-authorization-source)
  - [4.3 Data Currency](#43-data-currency)
- [5. Status Management](#5-status-management)
- [6. Design Rationale](#6-design-rationale)
- [7. Governance](#7-governance)
- [8. Implementation References](#8-implementation-references)

---

## 1. About this Document

This document defines the **Property Access Authorization Credential (PAAC)** schema — a structured data model for verifiable credentials that authorize a trust network member to access a homeowner's property portfolio data. Also referred to as a "Letter of Authorization." It is intended for governance reviewers, schema implementers, credential issuers, and verifier application developers evaluating whether this schema meets their requirements.

The PAAC schema captures a homeowner's consent to share specific property data categories with a specific recipient, at a defined access level, for a stated purpose.

### 1.1 Version History

| Ver.    | Date         | Notes                                                                 | Author(s)       |
|---------|--------------|-----------------------------------------------------------------------|-----------------|
| **1.0** | 18-Mar-2026  | Rewritten as schema documentation; governance body perspective        | Mathieu Glaude  |
| **0.9** | 26-Feb-2026  | Simplified; replaced `property_address` with `pid`; removed redundant DIDs; added access levels and relationship categories | Mathieu Glaude  |

[↑ Back to top](#table-of-contents)

---

## 2. Schema Overview

The Property Access Authorization Credential (PAAC) schema defines the data structure for a verifiable credential that authorizes a trust network member to access a homeowner's property portfolio data. It captures the homeowner's consent: what data categories the recipient can access, at what permission tier, for what purpose, and for how long. The PAAC enables access only — it does not contain the portfolio data itself.

|                        |                                                                                               |
|------------------------|-----------------------------------------------------------------------------------------------|
| **Schema:**            | Property Access Authorization v1.0                                                            |
| **Format:**            | W3C Verifiable Credentials (JSON-LD)                                                          |
| **Governance Body:**   | Cornerstone Network                                                                           |
| **Schema URI:**        | `https://trustinfrastructure.com/cornerstone/schemas/property-access-authorization.json`      |

### 2.1 Attribute Summary

| **#** | **Name**                  | **Attribute**                | **Data Type**        | **Required** |
|-------|---------------------------|------------------------------|----------------------|--------------|
| 001   | Authorization ID          | `authorization_id`           | String (UUID)        | Yes          |
| 002   | Homeowner ID              | `homeowner_id`               | DID / URI            | Yes          |
| 003   | PID                       | `pid`                        | String               | Yes          |
| 004   | Data Scope                | `data_scope`                 | JSON array (enums)   | Yes          |
| 005   | Authorization Purpose     | `authorization_purpose`      | String               | Yes          |
| 006   | Access Level              | `access_level`               | String (enum)        | Yes          |
| 007   | Start Date                | `start_date`                 | String (date)        | Yes          |
| 008   | Expiration Date           | `expiration_date`            | String (date) / null | No           |
| 009   | Authorization Evidence    | `authorization_evidence`     | String / URI         | Yes          |
| 010   | Relationship Category     | `relationship_category`      | String (enum)        | Yes          |

[↑ Back to top](#table-of-contents)

---

## 3. Schema Definition

### 3.1 Attributes

*Authorization ID (001)*

<table>
  <tr><th>Attribute</th><td><code>authorization_id</code></td></tr>
  <tr><th>Description</th><td>Platform-generated unique identifier for this authorization.</td></tr>
  <tr><th>Data Type</th><td>String (UUID)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>d4e5f6a7-8901-23bc-def0-456789012345</code></td></tr>
</table>

*Homeowner ID (002)*

<table>
  <tr><th>Attribute</th><td><code>homeowner_id</code></td></tr>
  <tr><th>Description</th><td>Cornerstone ID (DID/URI) of the homeowner granting access to their property portfolio data.</td></tr>
  <tr><th>Data Type</th><td>DID / URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>did:web:trustinfrastructure.com:cornerstone:id:b9e4c3d2</code></td></tr>
</table>

*PID (003)*

<table>
  <tr><th>Attribute</th><td><code>pid</code></td></tr>
  <tr><th>Description</th><td>Provincial land title Parcel Identifier for the property this authorization applies to. Resolves to the specific property without duplicating address data.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>027-263-975</code></td></tr>
</table>

*Data Scope (004)*

<table>
  <tr><th>Attribute</th><td><code>data_scope</code></td></tr>
  <tr><th>Description</th><td>Array of data category identifiers the recipient is authorized to access. See <a href="#32-enumerated-values">Enumerated Values</a> for valid options.</td></tr>
  <tr><th>Data Type</th><td>JSON array of enum strings</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>["ownership", "property_details", "valuations"]</code></td></tr>
</table>

*Authorization Purpose (005)*

<table>
  <tr><th>Attribute</th><td><code>authorization_purpose</code></td></tr>
  <tr><th>Description</th><td>Human-readable statement of the intent behind the data sharing authorization.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>Property listing and market analysis</code>, <code>Mortgage renewal assessment</code></td></tr>
</table>

*Access Level (006)*

<table>
  <tr><th>Attribute</th><td><code>access_level</code></td></tr>
  <tr><th>Description</th><td>Permission tier defining what the recipient can do with the accessed data. See <a href="#32-enumerated-values">Enumerated Values</a>.</td></tr>
  <tr><th>Data Type</th><td>String (enum)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>READ_ONLY</code>, <code>ADVISORY</code></td></tr>
</table>

*Start Date (007)*

<table>
  <tr><th>Attribute</th><td><code>start_date</code></td></tr>
  <tr><th>Description</th><td>Date from which the authorization is valid, in ISO 8601 format.</td></tr>
  <tr><th>Data Type</th><td>String (YYYY-MM-DD)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>2026-03-18</code></td></tr>
</table>

*Expiration Date (008)*

<table>
  <tr><th>Attribute</th><td><code>expiration_date</code></td></tr>
  <tr><th>Description</th><td>Optional end date for the authorization. Null means the authorization remains valid until explicitly revoked.</td></tr>
  <tr><th>Data Type</th><td>String (YYYY-MM-DD) or null</td></tr>
  <tr><th>Required</th><td>No</td></tr>
  <tr><th>Examples</th><td><code>2026-06-18</code> (90-day provider access), <code>null</code> (family trust network)</td></tr>
</table>

*Authorization Evidence (009)*

<table>
  <tr><th>Attribute</th><td><code>authorization_evidence</code></td></tr>
  <tr><th>Description</th><td>URI reference to the homeowner action log entry documenting consent.</td></tr>
  <tr><th>Data Type</th><td>String / URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>urn:cornerstone:evidence:e1f2a3b4-5678-90cd-ef01-234567890abc</code></td></tr>
</table>

*Relationship Category (010)*

<table>
  <tr><th>Attribute</th><td><code>relationship_category</code></td></tr>
  <tr><th>Description</th><td>The trust network member's relationship type to the homeowner. See <a href="#32-enumerated-values">Enumerated Values</a>.</td></tr>
  <tr><th>Data Type</th><td>String (enum)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>realtor</code>, <code>mortgage_broker</code>, <code>family_member</code></td></tr>
</table>

### 3.2 Enumerated Values

**Data Scope (`data_scope`) values:**

| Value              | Description                                           |
|--------------------|-------------------------------------------------------|
| `identity`         | Homeowner identity information                        |
| `ownership`        | Property ownership details                            |
| `property_details` | Physical property characteristics                     |
| `equity`           | Equity position (market value, mortgage balance)       |
| `costs`            | Property costs (taxes, maintenance, utilities)         |
| `insurance`        | Insurance policy information                          |
| `mortgage`         | Mortgage details and terms                            |
| `valuations`       | Property valuations and appraisals                    |
| `documents`        | Property documents (title, agreements)                |
| `full_portfolio`   | Complete portfolio access (all categories)             |

**Access Level (`access_level`) values:**

| Value           | Capability                                                             |
|-----------------|------------------------------------------------------------------------|
| `READ_ONLY`     | View data only                                                         |
| `OPERATIONAL`   | View + operational actions (e.g., maintenance logging)                 |
| `ADVISORY`      | View + advisory actions (e.g., market insights)                        |
| `TRANSACTIONAL` | View + transactional actions (e.g., authorize refinance initiation)    |

**Relationship Category (`relationship_category`) values:**

`realtor`, `mortgage_broker`, `family_member`, `accountant`, `lawyer`, `insurance_agent`, `property_manager`, `contractor`, `financial_advisor`, `other`

[↑ Back to top](#table-of-contents)

---

## 4. Data Source Requirements

### 4.1 Prerequisite Credentials

A PAAC can only be issued when all conditions are met:
- Homeowner holds a valid **Cornerstone ID**
- Homeowner holds a valid **Home Credential** for the referenced property (PID)
- Recipient holds a valid **Cornerstone ID**
- Scope, purpose, access level, and validity period are defined

### 4.2 Authorization Source

The authorization originates from the homeowner's explicit consent, captured in the platform flow. In the initial release pattern, PAACs may be auto-issued during professional-facilitated homeowner onboarding with consent captured in the platform flow.

### 4.3 Data Currency

- A PAAC reflects the authorization state at issuance time.
- Scope changes, purpose changes, or access level changes require revocation and re-issuance (no in-place updates).
- Typical validity patterns:
  - **Short-term provider access:** 30–90 days (insurance quotes, mortgage applications)
  - **Ongoing professional advisor:** 1–3 years (real estate professionals, financial advisors)
  - **Family trust network:** No expiration (perpetual family monitoring)

[↑ Back to top](#table-of-contents)

---

## 5. Status Management

This schema requires credentials to implement **W3C Bitstring Status List v1.1** for lifecycle management, with separate bitstrings for revocation and suspension.

| Event                              | Action   | Status Handling                           |
|------------------------------------|----------|-------------------------------------------|
| Homeowner grants access            | Issue    | Index set to 0 (valid)                    |
| Homeowner revokes access           | Revoke   | Revocation bit = 1 (permanent, immediate) |
| Expiration date reached            | Revoke   | Revocation bit = 1 (permanent)            |
| Property sold / Home Cred revoked  | Revoke   | Revocation bit = 1 (permanent)            |
| Homeowner Cornerstone ID revoked   | Revoke   | Revocation bit = 1 (permanent)            |
| Recipient Cornerstone ID revoked   | Revoke   | Revocation bit = 1 (permanent)            |
| Security incident                  | Suspend  | Suspension bit = 1 (reversible)           |
| Investigation cleared              | Reinstate| Suspension bit = 0                        |

**Cascade rules:**
- Homeowner Cornerstone ID revocation → all their PAACs revoked
- Recipient Cornerstone ID revocation → all PAACs they hold revoked
- Home Credential revocation → all PAACs for that specific property revoked (other properties unaffected)

**Revocation timing:** Immediate — no grace period.

[↑ Back to top](#table-of-contents)

---

## 6. Design Rationale

**`pid` replaces `property_address`:** Earlier versions included a full `property_address` JSON object. This created address triplication across Cornerstone ID, Home Credential, and PAAC. The PID is the canonical property identifier — address resolution happens at the application layer.

**`homeowner_id` as Cornerstone ID reference:** Repurposed from a platform UUID to a Cornerstone ID DID/URI, aligning with the ecosystem's chained credential model.

**Removed redundant DIDs:** `homeowner_did` and `tnm_did` were removed — the credential subject's `id` field and `homeowner_id` already capture both parties.

**Removed duplicate identifiers:** `tnm_id`, `property_id`, and `granted_date` were removed as duplicative of information available through credential subject, PID resolution, and `validFrom` envelope field respectively.

**Forbidden data:** This schema explicitly excludes portfolio data contents (equity, mortgages, valuations, insurance details), sensitive personal information (SINs, tax IDs, banking, credit scores), predicates, and assurance level indicators. The PAAC enables access — it does not transport data.

**Subject definition:** The credential subject is the **trust network member (recipient)**, not the homeowner. The homeowner is identified via `homeowner_id`.

[↑ Back to top](#table-of-contents)

---

## 7. Governance

- **Governance Body:** Cornerstone Network
- **Schema Owner:** Cornerstone Network
- **Review Cycle:** Annual, or upon breaking schema changes
- **Change Process:** Schema updates follow a change-managed governance process to ensure interoperability across all adopting organizations

### Scope Limitations

This credential:
- Does NOT grant property ownership rights or control
- Does NOT contain sensitive financial data (enables access only)
- Does NOT authorize portfolio changes unless `access_level` is explicitly set to `TRANSACTIONAL`

[↑ Back to top](#table-of-contents)

---

## 8. Implementation References

| Reference               | Value                                                                                          |
|--------------------------|------------------------------------------------------------------------------------------------|
| **Technical Format**     | W3C Verifiable Credentials Data Model (JSON-LD)                                                |
| **Schema URI**           | `https://trustinfrastructure.com/cornerstone/schemas/property-access-authorization.json`       |
| **Context URLs**         | `https://www.w3.org/ns/credentials/v2` <br/> `https://trustinfrastructure.com/cornerstone/contexts/property-access-authorization-v1.0.json` |
| **Schema Registry**      | `https://trustinfrastructure.com/cornerstone/schemas/`                                         |
| **Governance Doc**       | `https://openpropertyassociation.ca/credential-governance/property-access-authorization/`      |

### Required Envelope Fields

Credentials issued under this schema must include:
- `issuer` — DID of the issuing organization
- `validFrom` — authorization start (aligned with `start_date`)
- `validUntil` — if `expiration_date` is set
- `credentialSchema` — reference to this schema
- `credentialStatus` — revocation and suspension bitstring entries

### Credential Relationships

| Relationship | Credential |
|---|---|
| **Requires (homeowner)** | Cornerstone ID + Home Credential for referenced PID |
| **Requires (recipient)** | Cornerstone ID |
| **Independent of** | Professional Credential, Accreditation Credential, Portfolio Issuer |

[↑ Back to top](#table-of-contents)
