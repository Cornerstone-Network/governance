# Professional Credential — Schema Documentation

## Table of Contents

- [1. About this Document](#1-about-this-document)
  - [1.1 Version History](#11-version-history)
- [2. Schema Overview](#2-schema-overview)
  - [2.1 Attribute Summary](#21-attribute-summary)
- [3. Schema Definition](#3-schema-definition)
  - [3.1 Agent Contact Details](#31-agent-contact-details)
  - [3.2 Office Affiliation](#32-office-affiliation)
  - [3.3 Professional License (BC)](#33-professional-license-bc)
- [4. Data Source Requirements](#4-data-source-requirements)
  - [4.1 Agent Contact Details](#41-agent-contact-details)
  - [4.2 Office Affiliation](#42-office-affiliation)
  - [4.3 Professional License](#43-professional-license)
  - [4.4 Data Currency](#44-data-currency)
- [5. Status Management](#5-status-management)
- [6. Design Rationale](#6-design-rationale)
- [7. Governance](#7-governance)
- [8. Implementation References](#8-implementation-references)

---

## 1. About this Document

This document defines the **Professional Credential** schema — a structured data model for verifiable credentials that attest to a real estate professional's identity, office affiliation, and professional licensing. It is intended for governance reviewers, schema implementers, credential issuers, and verifier application developers evaluating whether this schema meets their requirements.

The Professional Credential schema is a comprehensive credential that captures who the professional is, where they operate, and under what authority they are licensed — providing a complete professional profile in a single verifiable credential.

### 1.1 Version History

| Ver.    | Date         | Notes                                                                 | Author(s)       |
|---------|--------------|-----------------------------------------------------------------------|-----------------|
| **1.0** | 28-Mar-2026  | Complete revamp: 3 attribute categories (Agent Contact Details, Office Affiliation, Professional License); 18 attributes; re-combines licensing with professional identity | Mathieu Glaude  |
| **0.4** | 18-Mar-2026  | Rewritten as schema documentation; v0.3 attribute structure; governance body perspective | Mathieu Glaude  |
| **0.3** | 26-Feb-2026  | Simplified to 6 attributes; separated licensing into Accreditation Credential; cornerstone_id reference | Mathieu Glaude  |
| **0.2** | 12-Nov-2025  | Restructured into Real Estate Professional with 3 attribute categories | Mathieu Glaude  |
| **0.1** | 3-Sep-2025   | Initial draft                                                         | Mathieu Glaude  |

[↑ Back to top](#table-of-contents)

---

## 2. Schema Overview

The Professional Credential schema defines the data structure for a verifiable credential that provides a complete professional profile for a real estate professional. It combines agent contact information, office affiliation, and professional licensing into a single credential, enabling verifiers to confirm a professional's identity, where they operate, and their regulatory standing.

|                        |                                                                                         |
|------------------------|-----------------------------------------------------------------------------------------|
| **Schema:**            | Professional Credential v1.0                                                            |
| **Format:**            | W3C Verifiable Credentials (JSON-LD)                                                    |
| **Governance Body:**   | Cornerstone Network                                                                     |
| **Schema URI:**        | `https://trustinfrastructure.com/cornerstone/schemas/professional-credential.json`      |

### 2.1 Attribute Summary

**Agent Contact Details**

| **#** | **Name**            | **Attribute**          | **Data Type**         | **Required** |
|-------|---------------------|------------------------|-----------------------|--------------|
| 001   | First Name          | `first_name`           | String                | Yes          |
| 002   | Last Name           | `last_name`            | String                | Yes          |
| 003   | Telephone Number    | `telephone`            | String (regex)        | Yes          |
| 004   | Email Address       | `email`                | String (regex)        | Yes          |
| 005   | PREC Indicator      | `prec`                 | Enum (Y/N)            | Yes          |

> **Note:** When PREC = "Y", the credential card displays the static text "Personal Real Estate Corporation".

**Office Affiliation**

| **#** | **Name**            | **Attribute**          | **Data Type**         | **Required** |
|-------|---------------------|------------------------|-----------------------|--------------|
| 006   | Franchise           | `franchise`            | String                | Yes          |
| 007   | Operating Office    | `operating_office`     | String                | Yes          |
| 008   | Operating Address   | `operating_address`    | String (Canada Post)  | Yes          |
| 009   | Title               | `title`                | String                | Yes          |
| 010   | Since (Year)        | `since_year`           | String (YYYY)         | Yes          |

**Professional License (BC)**

| **#** | **Name**            | **Attribute**          | **Data Type**         | **Required** |
|-------|---------------------|------------------------|-----------------------|--------------|
| 011   | License Number      | `license_number`       | String (NNNNNN)       | Yes          |
| 012   | License Effective Date | `license_effective_date` | String (DD-MM-YYYY) | Yes        |
| 013   | License Expiry Date | `license_expiry_date`  | String (DD-MM-YYYY)   | Yes          |
| 014   | Licensed As         | `licensed_as`          | Enum                  | Yes          |
| 015   | Licensed For        | `licensed_for`         | Enum (multi-select)   | Yes          |
| 016   | PREC                | `prec`                 | Enum (Y/N)            | Yes          |
| 017   | Business Name       | `business_name`        | String                | No           |
| 018   | Issuing Authority   | `issuing_authority`    | Enum                  | Yes          |

[↑ Back to top](#table-of-contents)

---

## 3. Schema Definition

### 3.1 Agent Contact Details

*First Name (001)*

<table>
  <tr><th>Attribute</th><td><code>first_name</code></td></tr>
  <tr><th>Description</th><td>The professional's given name.</td></tr>
  <tr><th>Data Type</th><td>String (free text)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>Sarah</code>, <code>James</code></td></tr>
</table>

*Last Name (002)*

<table>
  <tr><th>Attribute</th><td><code>last_name</code></td></tr>
  <tr><th>Description</th><td>The professional's family name.</td></tr>
  <tr><th>Data Type</th><td>String (free text)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>Chen</code>, <code>Williams</code></td></tr>
</table>

*Telephone Number (003)*

<table>
  <tr><th>Attribute</th><td><code>telephone</code></td></tr>
  <tr><th>Description</th><td>The professional's contact telephone number.</td></tr>
  <tr><th>Data Type</th><td>String (regular expression validated)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Format</th><td>E.164 or North American format</td></tr>
  <tr><th>Examples</th><td><code>+16045551234</code>, <code>(604) 555-1234</code></td></tr>
</table>

*Email Address (004)*

<table>
  <tr><th>Attribute</th><td><code>email</code></td></tr>
  <tr><th>Description</th><td>The professional's contact email address.</td></tr>
  <tr><th>Data Type</th><td>String (regular expression validated)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>sarah.chen@suttonrealty.com</code></td></tr>
</table>

*PREC Indicator (005)*

<table>
  <tr><th>Attribute</th><td><code>prec</code></td></tr>
  <tr><th>Description</th><td>Indicates whether the professional operates through a Personal Real Estate Corporation (PREC). When "Y", the credential card displays the static text "Personal Real Estate Corporation".</td></tr>
  <tr><th>Data Type</th><td>Enum</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Values</th><td><code>Y</code>, <code>N</code></td></tr>
</table>

### 3.2 Office Affiliation

*Franchise (006)*

<table>
  <tr><th>Attribute</th><td><code>franchise</code></td></tr>
  <tr><th>Description</th><td>The real estate franchise or brand the professional is affiliated with.</td></tr>
  <tr><th>Data Type</th><td>String (free text)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>Sutton Group</code>, <code>Royal LePage</code>, <code>RE/MAX</code></td></tr>
</table>

*Operating Office (007)*

<table>
  <tr><th>Attribute</th><td><code>operating_office</code></td></tr>
  <tr><th>Description</th><td>The specific office or branch where the professional operates.</td></tr>
  <tr><th>Data Type</th><td>String (free text)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>Sutton Group - West Coast Realty</code>, <code>Royal LePage Sterling Realty</code></td></tr>
</table>

*Operating Address (008)*

<table>
  <tr><th>Attribute</th><td><code>operating_address</code></td></tr>
  <tr><th>Description</th><td>The physical address of the operating office, validated against the Canada Post address database.</td></tr>
  <tr><th>Data Type</th><td>String (Canada Post API validated)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>4555 Kingsway, Burnaby, BC V5H 4T8</code></td></tr>
</table>

*Title (009)*

<table>
  <tr><th>Attribute</th><td><code>title</code></td></tr>
  <tr><th>Description</th><td>The professional's title or role within the office.</td></tr>
  <tr><th>Data Type</th><td>String (free text)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>Real Estate Professional</code>, <code>Managing Broker</code>, <code>Associate Broker</code></td></tr>
</table>

*Since Year (010)*

<table>
  <tr><th>Attribute</th><td><code>since_year</code></td></tr>
  <tr><th>Description</th><td>The year the professional began their affiliation with the office.</td></tr>
  <tr><th>Data Type</th><td>String (YYYY, regular expression validated)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>2018</code>, <code>2023</code></td></tr>
</table>

### 3.3 Professional License (BC)

*License Number (011)*

<table>
  <tr><th>Attribute</th><td><code>license_number</code></td></tr>
  <tr><th>Description</th><td>The professional's BC real estate license number, issued by the regulatory authority.</td></tr>
  <tr><th>Data Type</th><td>String (6-digit numeric: NNNNNN)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>123456</code>, <code>078901</code></td></tr>
</table>

*License Effective Date (012)*

<table>
  <tr><th>Attribute</th><td><code>license_effective_date</code></td></tr>
  <tr><th>Description</th><td>The date the professional's license became effective.</td></tr>
  <tr><th>Data Type</th><td>String (DD-MM-YYYY)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>15-03-2020</code></td></tr>
</table>

*License Expiry Date (013)*

<table>
  <tr><th>Attribute</th><td><code>license_expiry_date</code></td></tr>
  <tr><th>Description</th><td>The date the professional's license expires and must be renewed.</td></tr>
  <tr><th>Data Type</th><td>String (DD-MM-YYYY)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Examples</th><td><code>14-03-2026</code></td></tr>
</table>

*Licensed As (014)*

<table>
  <tr><th>Attribute</th><td><code>licensed_as</code></td></tr>
  <tr><th>Description</th><td>The category of license held by the professional.</td></tr>
  <tr><th>Data Type</th><td>Enum</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Values</th><td><code>Representative</code>, <code>Associate Broker</code>, <code>Managing Broker</code></td></tr>
</table>

*Licensed For (015)*

<table>
  <tr><th>Attribute</th><td><code>licensed_for</code></td></tr>
  <tr><th>Description</th><td>The service categories the professional is licensed to perform. A professional may hold licenses for multiple service categories.</td></tr>
  <tr><th>Data Type</th><td>Enum (multi-select)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Values</th><td><code>Trading Services</code>, <code>Rental Property Management Services</code>, <code>Strata Managing Services</code></td></tr>
</table>

*PREC (016)*

<table>
  <tr><th>Attribute</th><td><code>prec</code></td></tr>
  <tr><th>Description</th><td>Indicates whether the professional operates through a Personal Real Estate Corporation (PREC).</td></tr>
  <tr><th>Data Type</th><td>Enum</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Values</th><td><code>Y</code>, <code>N</code></td></tr>
</table>

*Business Name (017)*

<table>
  <tr><th>Attribute</th><td><code>business_name</code></td></tr>
  <tr><th>Description</th><td>The name of the Personal Real Estate Corporation or business entity, if applicable. Required when PREC = "Y".</td></tr>
  <tr><th>Data Type</th><td>String (free text)</td></tr>
  <tr><th>Required</th><td>No (conditional — required when PREC = "Y")</td></tr>
  <tr><th>Examples</th><td><code>Chen Realty Corp.</code></td></tr>
</table>

*Issuing Authority (018)*

<table>
  <tr><th>Attribute</th><td><code>issuing_authority</code></td></tr>
  <tr><th>Description</th><td>The regulatory body that issued the professional license.</td></tr>
  <tr><th>Data Type</th><td>Enum</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Values</th><td><code>BCFSA</code> (BC Financial Services Authority)</td></tr>
</table>

[↑ Back to top](#table-of-contents)

---

## 4. Data Source Requirements

Credentials issued under this schema are expected to draw from the following authoritative sources.

### 4.1 Agent Contact Details

Contact information (`first_name`, `last_name`, `telephone`, `email`) is provided by the professional and verified by the issuing organization. The PREC indicator is sourced from the regulatory authority's records.

### 4.2 Office Affiliation

Office affiliation data (`franchise`, `operating_office`, `operating_address`, `title`, `since_year`) is provided by the employer's system of record (e.g., brokerage management platform). The employer confirms the professional's active affiliation out-of-band before a credential is issued. The `operating_address` is validated against the Canada Post address database to ensure standardized, deliverable addresses.

### 4.3 Professional License

Licensing data (`license_number`, `license_effective_date`, `license_expiry_date`, `licensed_as`, `licensed_for`, `issuing_authority`) is sourced from the regulatory authority (BCFSA for British Columbia). The `business_name` is sourced from the PREC registry when applicable.

### 4.4 Data Currency

- A credential reflects the state of the professional's contact details, office affiliation, and licensing at the time of issuance.
- Changes to any of the following require credential revocation and re-issuance: office affiliation, license status, licensed categories, or PREC status.
- Contact detail updates (telephone, email) may be handled via credential refresh without revocation, at the issuer's discretion.

[↑ Back to top](#table-of-contents)

---

## 5. Status Management

This schema requires credentials to implement **W3C Bitstring Status List v1.1** for lifecycle management, with separate bitstrings for revocation and suspension.

| Event                    | Action              | Status Handling                           |
|--------------------------|---------------------|-------------------------------------------|
| Credential issued        | Issue credential    | Index set to 0 (valid)                    |
| Employment terminated    | Revoke              | Revocation bit = 1 (permanent)            |
| Office change            | Revoke + re-issue   | Old revoked; new credential index = 0     |
| License expired          | Revoke              | Revocation bit = 1 (permanent)            |
| License renewed          | Revoke + re-issue   | Old revoked; new credential with updated dates |
| License suspended        | Suspend             | Suspension bit = 1 (reversible)           |
| License reinstated       | Reinstate           | Suspension bit = 0                        |
| Fraud detected           | Revoke              | Revocation bit = 1 (permanent)            |

[↑ Back to top](#table-of-contents)

---

## 6. Design Rationale

**Re-combination of professional identity and licensing (v1.0):** Versions 0.3–0.4 separated organizational affiliation from professional licensing into two independent credentials (Professional Credential and Accreditation Credential). After stakeholder feedback and real-world implementation planning, these have been re-combined into a single comprehensive Professional Credential. The rationale: in practice, a real estate professional's identity, office affiliation, and license status are verified together and consumed together. Splitting them created unnecessary complexity for issuers and verifiers without meaningful benefit.

**Three attribute categories:** The schema is organized into logical groups — Agent Contact Details, Office Affiliation, and Professional License — making it clear where each data point originates and how it should be verified.

**PREC as a first-class attribute:** The Personal Real Estate Corporation indicator is important in BC real estate. When PREC = "Y", the credential card displays "Personal Real Estate Corporation" as static text, and the `business_name` field becomes conditionally required.

**Canada Post API for addresses:** The `operating_address` field requires validation against the Canada Post address database, ensuring standardized and deliverable addresses across all credentials.

**Multi-select for Licensed For:** A professional may be licensed for multiple service categories simultaneously (e.g., both Trading Services and Rental Property Management Services). The multi-select enum supports this without requiring multiple credentials.

**BC-specific licensing section:** The Professional License section is scoped to BC (BCFSA) as the initial jurisdiction. Future versions may add jurisdiction-specific sections for other provinces or US states.

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