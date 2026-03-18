# Portfolio Issuer — Schema Documentation

## Table of Contents

- [1. About this Document](#1-about-this-document)
  - [1.1 Version History](#11-version-history)
- [2. Schema Overview](#2-schema-overview)
  - [2.1 Attribute Summary](#21-attribute-summary)
- [3. Schema Definition](#3-schema-definition)
  - [3.1 Attributes](#31-attributes)
- [4. Data Source Requirements](#4-data-source-requirements)
  - [4.1 Identity Binding](#41-identity-binding)
  - [4.2 Authorization Source](#42-authorization-source)
  - [4.3 Data Currency](#43-data-currency)
- [5. Status Management](#5-status-management)
- [6. Design Rationale](#6-design-rationale)
- [7. Governance](#7-governance)
- [8. Implementation References](#8-implementation-references)

---

## 1. About this Document

This document defines the **Portfolio Issuer** schema — a structured data model for verifiable credentials that authorize professionals to create homeowner portfolios, issue credentials, and approve homeowner onboarding to the Cornerstone Network on behalf of Network Partners. It is intended for governance reviewers, schema implementers, credential issuers, and verifier application developers evaluating whether this schema meets their requirements.

The Portfolio Issuer schema captures a delegation of authority from a Network Partner to an individual professional, defining what they are authorized to do within the ecosystem.

### 1.1 Version History

| Ver.    | Date         | Notes                                                                 | Author(s)       |
|---------|--------------|-----------------------------------------------------------------------|-----------------|
| **1.0** | 18-Mar-2026  | Rewritten as schema documentation; governance body perspective        | Mathieu Glaude  |
| **0.9** | 26-Feb-2026  | Simplified to 7 attributes; removed redundant fields                  | Mathieu Glaude  |

[↑ Back to top](#table-of-contents)

---

## 2. Schema Overview

The Portfolio Issuer schema defines the data structure for a verifiable credential that authorizes a professional to create homeowner portfolios, issue Cornerstone ID and Home Credentials, and approve homeowner onboarding on behalf of a Network Partner. This schema captures the delegation of authority — not the professional's identity or licensing status, which are handled by the Cornerstone ID and Accreditation Credential schemas respectively.

|                        |                                                                                     |
|------------------------|-------------------------------------------------------------------------------------|
| **Schema:**            | Portfolio Issuer v1.0                                                               |
| **Format:**            | W3C Verifiable Credentials (JSON-LD)                                                |
| **Governance Body:**   | Cornerstone Network                                                                 |
| **Schema URI:**        | `https://trustinfrastructure.com/cornerstone/schemas/portfolio-issuer.json`         |

### 2.1 Attribute Summary

| **#** | **Name**                | **Attribute**             | **Data Type**     | **Required** |
|-------|-------------------------|---------------------------|-------------------|--------------|
| 001   | Cornerstone ID Ref      | `cornerstone_id`          | DID / URI         | Yes          |
| 002   | Portfolio Issuer ID     | `portfolio_issuer_id`     | String (UUID)     | Yes          |
| 003   | Network Partner ID      | `network_partner_id`      | String (UUID)     | Yes          |
| 004   | Network Partner Name    | `network_partner_name`    | String            | Yes          |
| 005   | Granted Date            | `granted_date`            | String (date)     | Yes          |
| 006   | Granting Authority      | `granting_authority`      | String            | Yes          |
| 007   | Scope                   | `scope`                   | JSON object       | No           |

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

*Portfolio Issuer ID (002)*

<table>
  <tr><th>Attribute</th><td><code>portfolio_issuer_id</code></td></tr>
  <tr><th>Description</th><td>Platform-generated unique identifier for this portfolio issuer authorization.</td></tr>
  <tr><th>Data Type</th><td>String (UUID)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>7f3a9b2c-4d5e-6f78-90ab-cdef12345678</code></td></tr>
</table>

*Network Partner ID (003)*

<table>
  <tr><th>Attribute</th><td><code>network_partner_id</code></td></tr>
  <tr><th>Description</th><td>Identifier of the Network Partner granting portfolio issuer authority.</td></tr>
  <tr><th>Data Type</th><td>String (UUID)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>b2c3d4e5-f678-90ab-cdef-123456789abc</code></td></tr>
</table>

*Network Partner Name (004)*

<table>
  <tr><th>Attribute</th><td><code>network_partner_name</code></td></tr>
  <tr><th>Description</th><td>Human-readable name of the Network Partner.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>Sutton Group</code>, <code>Royal LePage</code></td></tr>
</table>

*Granted Date (005)*

<table>
  <tr><th>Attribute</th><td><code>granted_date</code></td></tr>
  <tr><th>Description</th><td>Date the portfolio issuer authority was granted, in ISO 8601 format.</td></tr>
  <tr><th>Data Type</th><td>String (YYYY-MM-DD)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>2026-03-15</code></td></tr>
</table>

*Granting Authority (006)*

<table>
  <tr><th>Attribute</th><td><code>granting_authority</code></td></tr>
  <tr><th>Description</th><td>Entity that granted the portfolio issuer authority — typically the Network Partner name or "Cornerstone Network" for direct grants.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>Sutton Group</code>, <code>Cornerstone Network</code></td></tr>
</table>

*Scope (007)*

<table>
  <tr><th>Attribute</th><td><code>scope</code></td></tr>
  <tr><th>Description</th><td>Optional geographic or organizational restrictions on the portfolio issuer's authority. Contains arrays for <code>regions</code>, <code>offices</code>, <code>service_areas</code>, and <code>property_types</code>.</td></tr>
  <tr><th>Data Type</th><td>JSON object</td></tr>
  <tr><th>Required</th><td>No</td></tr>
  <tr><th>Examples</th><td><code>{"regions": ["BC"], "offices": ["Surrey"], "service_areas": [], "property_types": []}</code></td></tr>
</table>

[↑ Back to top](#table-of-contents)

---

## 4. Data Source Requirements

Credentials issued under this schema are expected to draw from the following authoritative sources.

### 4.1 Identity Binding

The `cornerstone_id` attribute references a separately issued Cornerstone ID credential. This schema does not embed identity attributes directly.

### 4.2 Authorization Source

Authorization data (`network_partner_id`, `network_partner_name`, `granted_date`, `granting_authority`, `scope`) is provided by the Network Partner's administrative decision. The Network Partner confirms the professional's authorization to act on their behalf before a credential is issued.

### 4.3 Data Currency

- A credential reflects the authorization state at the time of issuance.
- Authorization termination, scope changes, or Network Partner membership changes require credential revocation.
- Expiration period: 1–3 years (per Network Partner or Cornerstone Network policy).

[↑ Back to top](#table-of-contents)

---

## 5. Status Management

This schema requires credentials to implement **W3C Bitstring Status List v1.1** for lifecycle management, with separate bitstrings for revocation and suspension.

| Event                           | Action              | Status Handling                           |
|---------------------------------|---------------------|-------------------------------------------|
| Authorization granted           | Issue credential    | Index set to 0 (valid)                    |
| Authorization terminated by NP  | Revoke              | Revocation bit = 1 (permanent)            |
| Organization affiliation ends   | Revoke              | Revocation bit = 1 (permanent)            |
| Cornerstone ID revoked          | Revoke              | Revocation bit = 1 (permanent)            |
| Investigation pending           | Suspend             | Suspension bit = 1 (reversible)           |
| Investigation cleared           | Reinstate           | Suspension bit = 0                        |
| Scope change                    | Revoke + re-issue   | Old revoked; new credential index = 0     |
| NP membership ends              | Revoke              | Revocation bit = 1 (permanent)            |

**Post-revocation:** Existing portfolios created by the holder remain valid. New portfolio creation is blocked.

[↑ Back to top](#table-of-contents)

---

## 6. Design Rationale

**Removed `canIssuePortfolios`:** This boolean was always `true` — the credential's existence already indicates authorization. Redundant boolean fields are an anti-pattern.

**Removed `professional_credential_ref`:** Following the independent credential design principle, the Portfolio Issuer does not reference the Professional Credential. A holder may possess one, the other, or both.

**Removed `issuer_type`:** Single type for initial release. If multiple issuer types emerge, the schema can be extended with a minor version bump.

**Removed `portfolio_issuer_evidence`:** Evidence moved to the credential envelope's `evidence` array per W3C standard practice, rather than duplicating as a credential subject attribute.

**Capabilities granted by this credential:** Portfolio creation authority, homeowner approval, Cornerstone ID and Home Credential issuance, data source integration initiation, portfolio branding rights, referral fee earning eligibility.

[↑ Back to top](#table-of-contents)

---

## 7. Governance

- **Governance Body:** Cornerstone Network
- **Schema Owner:** Cornerstone Network
- **Review Cycle:** Annual, or upon breaking schema changes
- **Change Process:** Schema updates follow a change-managed governance process to ensure interoperability across all adopting organizations

### Credential Independence

This schema operates independently of the Professional Credential and Accreditation Credential schemas. A holder may possess a Portfolio Issuer credential without holding either of those.

[↑ Back to top](#table-of-contents)

---

## 8. Implementation References

| Reference               | Value                                                                                |
|--------------------------|--------------------------------------------------------------------------------------|
| **Technical Format**     | W3C Verifiable Credentials Data Model (JSON-LD)                                      |
| **Schema URI**           | `https://trustinfrastructure.com/cornerstone/schemas/portfolio-issuer.json`          |
| **Context URLs**         | `https://www.w3.org/ns/credentials/v2` <br/> `https://trustinfrastructure.com/cornerstone/contexts/portfolio-issuer-v1.0.json` |
| **Schema Registry**      | `https://trustinfrastructure.com/cornerstone/schemas/`                               |
| **Governance Doc**       | `https://openpropertyassociation.ca/credential-governance/portfolio-issuer/`         |

### Required Envelope Fields

Credentials issued under this schema must include:
- `issuer` — DID of the issuing organization
- `validFrom` and `validUntil` — temporal bounds (1–3 year expiration)
- `credentialSchema` — reference to this schema
- `credentialStatus` — revocation and suspension bitstring entries

### Credential Relationships

| Relationship | Credential |
|---|---|
| **References** | Cornerstone ID |
| **Independent of** | Professional Credential, Accreditation Credential |

[↑ Back to top](#table-of-contents)
