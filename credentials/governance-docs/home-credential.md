# Home Credential – Credential Governance

## 1. About this Document

This document describes the **Home Credential** (also referred to as "Verified Homeowner") verifiable credential to help potential verifiers determine whether it is suitable for their needs. The intended audience includes real estate professionals, lenders, insurers, municipalities, and financial service providers.

The Home Credential is issued by the **Cornerstone Network** to Canadian homeowners and represents authoritative proof of homeownership derived from the provincial land title registry, combined with high-assurance identity verification sources.

### 1.1 Version History

| Ver.      | Date        | Notes                               | Author(s) |
|-----------|-------------|-------------------------------------|-------------|
| **1.0**   | 26-Feb-2026 | JSON-LD format finalization; replaced identity attributes with `cornerstone_id` reference for atomicity; removed `effective_year`; updated schema hosting; added design rationale and industry references | Mathieu Glaude |
| **0.3**   | 29-Sep-2025 | Updated credential schema information based on available attributes from Landcor | Mathieu Glaude |
| **0.2**   | 3-Sep-2025  | Simplified and reformatted document  | Mathieu Glaude |
| **0.1**   | 20-Aug-2025 | Initial draft                        | Mathieu Glaude |

## 2. Credential Overview

The Home Credential is a verifiable credential (VC) issued to individuals who are confirmed as registered property owners in the provincial land-title registry. It enables the holder to prove property ownership while sharing only the minimum data required.

Each issuance is backed by **FINTRAC-compliant dual-process checks**:
- Interac Bank Verification or BC Person Credential, and
- Land Title name-and-address match.

The credential references the holder's **Cornerstone ID** for identity binding, rather than duplicating identity claims. This follows the atomicity principle: each credential contains only the data it uniquely attests to.

The credential is issued directly into the **Cornerstone Wallet** and can be consumed by relying parties without requiring them to re-run identity checks.

|              |                                                                 |
|--------------|-----------------------------------------------------------------|
| **Credential:** | Home Credential (Verified Homeowner)                       |
| **Schema:**     | Home Credential v1.0                                       |
| **Issuer:**     | Cornerstone Network                                        |
| **Issuer DID:** | TBD (e.g., `did:web:trustinfrastructure.com:cornerstone`)  |
| **Format:**     | W3C Verifiable Credentials (JSON-LD)                       |

**Important**: This credential represents ownership of a **specific property**. Homeowners who own multiple properties receive **separate Home Credentials for each property** — one credential per property owned. Each property has its own property-specific trust network.

### 2.1 Attribute Summary (10 Attributes)

| **#** | **Name**            | **Attribute**          | **Data Type**       | **Required** | **Notes** |
|-------|---------------------|------------------------|---------------------|--------------|-----------|
| 001   | Cornerstone ID Reference | `cornerstone_id` | DID/URI             | Yes          | Reference to holder's Cornerstone ID. |
| 002   | PID                 | `pid`                  | String              | Yes          | Provincial land title registry identifier. |
| 003   | Property Address    | `property_address`     | JSON object         | Yes          | Standardized postal address (street_address, locality, region, postal_code, country). |
| 004   | Jurisdiction        | `jurisdiction`         | String              | Yes          | Province of registration (e.g., BC, ON). |
| 005   | Purchase Price      | `purchase_price`       | Number              | Yes          | From Landcor / land title sales history. |
| 006   | Purchase Date       | `purchase_date`        | String (YYYY-MM-DD) | Yes          | ISO 8601 date. |
| 007   | Year Built          | `year_built`           | Integer             | Yes          | From Landcor / property details. |
| 008   | Neighbourhood       | `neighbourhood`        | String              | Yes          | From Landcor / property details. |
| 009   | Identity Evidence   | `identity_evidence`    | String / URI        | Yes          | Reference to identity verification record. |
| 010   | Title Evidence      | `title_evidence`       | String / URI        | Yes          | Reference to land title verification record. |

### 2.2 Design Rationale

**Attribute decisions in this version:**

- **Replaced 4 identity attributes with `cornerstone_id` reference.** Previously, the Home Credential contained `given_names`, `family_name`, `birthdate_dateint`, and `verified_email` — all duplicated from the Cornerstone ID. These have been replaced with a single `cornerstone_id` reference. This follows the atomicity principle: each credential contains only the data it uniquely attests to. The Professional and Accreditation credentials already use this reference pattern. When a verifier needs both identity and property data, they request both credentials.

- **Removed `effective_year`.** This was a BC-specific assessment concept (renovation-adjusted year) with limited value for verifiers outside British Columbia. Not needed for v1.

- **`purchase_price` and `purchase_date` retained in the credential.** These are intentionally included as verified data from land title registry records captured at issuance time. They represent facts about the property transaction that are useful for fraud checks, appraisals, and loan risk assessment.

**Industry references:**

- **vLEI (GLEIF)**: Uses a chained credential model where credentials reference parent credentials via "edges" rather than duplicating attributes. The Home Credential follows this same principle — the `cornerstone_id` reference creates the chain while keeping each credential atomic.

### 2.3 Removed Attributes (v1.0)

| Attribute | Reason for Removal |
|-----------|-------------------|
| `given_names` (String) | Replaced by `cornerstone_id` reference. Identity data lives in the Cornerstone ID; duplicating it violates the atomicity principle. |
| `family_name` (String) | Replaced by `cornerstone_id` reference. |
| `birthdate_dateint` (Integer) | Replaced by `cornerstone_id` reference. Also, integer format was AnonCreds-specific. |
| `verified_email` (String) | Replaced by `cornerstone_id` reference. |
| `effective_year` (Integer) | BC-specific assessment concept with limited value for v1 verifiers. |

## 3. Credential Details

### 3.1 Issuer

The Home Credential is issued by the **Cornerstone Network**, acting as the credential authority. Each credential is issued only after both identity and title checks are successfully completed.

### 3.2 Schema and Credential Definition Governance

The Home Credential schema and definition are managed by the Cornerstone Network and registered on the **Cornerstone Trust Registry**. Updates follow a change-managed governance process to ensure interoperability.

### 3.3 Issuer Data Source

The data comes from two authoritative categories:

- **Identity Verification**: Interac Bank Verification Service, or BC Person Credential.
- **Title Verification**: Provincial land-title registry records (via Landcor), including property identifier, civic address, ownership type, and percentage.

Together these bind a person's verified identity (via Cornerstone ID) to their legal property ownership.

#### 3.3.1 Data Updates
- A credential reflects the state of records at issuance.
- Ownership or identity changes require revocation and re-issuance.
- Evidence is retained for five years in compliance with FINTRAC.

### 3.4 Assurance

The credential combines two assurance anchors:
- High-assurance identity (via Cornerstone ID, verified through Interac or BC Person Credential)
- Land-title registry record (ownership)

This meets FINTRAC's dual-process method.

### 3.5 Credential Status

Credential status is managed via the **W3C Bitstring Status List v1.1**, supporting both `revocation` (permanent) and `suspension` (reversible) purposes.

### 3.6 Revocation

A Home Credential will be revoked in cases such as:
1. Property sold or ownership updated
2. Cornerstone ID (referenced credential) is revoked or expired
3. Identity evidence expires
4. Fraud or errors detected
5. Holder requests re-issuance
6. Regulatory or legal request

**Cascade Revocation**: When a Home Credential is revoked, all PAACs for that specific property should be revoked. This is managed operationally by the Cornerstone Network platform. Other properties owned by the same homeowner are unaffected (each property has its own Home Credential).

Re-issuance involves re-verification and issuance of a new credential to the holder's Cornerstone Wallet.

## 4. Credential Definition

### 4.1 Credential Schema

- **Schema ID (URI):** `https://trustinfrastructure.com/cornerstone/schemas/home-credential.json` *(exact URL TBD)*
- **Schema Versioning:** Semantic versioning. Breaking changes produce a new major version.
- **Contexts:**
  - `https://www.w3.org/ns/credentials/v2`
  - `https://trustinfrastructure.com/cornerstone/contexts/home-credential-v1.json` *(exact URL TBD)*

### 4.2 Subject of the Credential

The subject is the **individual holder**, bound to a **specific property** at issuance. Binding is confirmed by:
- Reference to verified Cornerstone ID (via `cornerstone_id`)
- Land-title registry name/address match

### 4.3 Attributes

#### 4.3.1 Identity Reference

*Cornerstone ID Reference (001)*

<table>
  <tr><th>Attribute</th><td><code>cornerstone_id</code></td></tr>
  <tr><th>Description</th><td>Reference to the holder's verified Cornerstone ID credential. Replaces previously duplicated identity attributes.</td></tr>
  <tr><th>Source</th><td>Cornerstone ID issuance.</td></tr>
  <tr><th>Data Type</th><td>DID/URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
  <tr><th>Design Note</th><td>Follows atomicity principle. Identity data lives in Cornerstone ID; this credential references it rather than duplicating it.</td></tr>
</table>

#### 4.3.2 Property & Ownership Attributes

*PID (002)*

<table>
  <tr><th>Attribute</th><td><code>pid</code></td></tr>
  <tr><th>Description</th><td>Unique Parcel Identifier (PID) used in land registries.</td></tr>
  <tr><th>Source</th><td>Provincial Land Title Registry / Landcor feed.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>027-263-975</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Property Address (003)*

<table>
  <tr><th>Attribute</th><td><code>property_address</code></td></tr>
  <tr><th>Description</th><td>Standardized postal address including street, locality, region, and postal code.</td></tr>
  <tr><th>Source</th><td>Landcor "Full Property Address".</td></tr>
  <tr><th>Data Type</th><td>JSON object containing: <code>street_address</code>, <code>locality</code>, <code>region</code>, <code>postal_code</code>, <code>country</code></td></tr>
  <tr><th>Examples</th><td><code>{"street_address": "1234 W 10th Ave", "locality": "Vancouver", "region": "BC", "postal_code": "V6H 1J9", "country": "CA"}</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Jurisdiction (004)*

<table>
  <tr><th>Attribute</th><td><code>jurisdiction</code></td></tr>
  <tr><th>Description</th><td>Province where the property is registered.</td></tr>
  <tr><th>Source</th><td>Landcor property details.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>BC</code>, <code>ON</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Purchase Price (005)*

<table>
  <tr><th>Attribute</th><td><code>purchase_price</code></td></tr>
  <tr><th>Description</th><td>Last recorded sale price of the property.</td></tr>
  <tr><th>Source</th><td>Landcor sales history feed.</td></tr>
  <tr><th>Data Type</th><td>Number</td></tr>
  <tr><th>Examples</th><td><code>1250000</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Purchase Date (006)*

<table>
  <tr><th>Attribute</th><td><code>purchase_date</code></td></tr>
  <tr><th>Description</th><td>Date of the last property sale transaction.</td></tr>
  <tr><th>Source</th><td>Landcor sales history feed.</td></tr>
  <tr><th>Data Type</th><td>String (YYYY-MM-DD)</td></tr>
  <tr><th>Examples</th><td><code>2018-05-14</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Year Built (007)*

<table>
  <tr><th>Attribute</th><td><code>year_built</code></td></tr>
  <tr><th>Description</th><td>Year the property was originally constructed.</td></tr>
  <tr><th>Source</th><td>Landcor property details.</td></tr>
  <tr><th>Data Type</th><td>Integer</td></tr>
  <tr><th>Examples</th><td><code>1987</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Neighbourhood (008)*

<table>
  <tr><th>Attribute</th><td><code>neighbourhood</code></td></tr>
  <tr><th>Description</th><td>Recognized neighbourhood/community where the property is located.</td></tr>
  <tr><th>Source</th><td>Landcor property details.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>Kitsilano</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

#### 4.3.3 Evidence Attributes

*Identity Evidence (009)*

<table>
  <tr><th>Attribute</th><td><code>identity_evidence</code></td></tr>
  <tr><th>Description</th><td>UUID referencing IDV source(s), author, and date of verification.</td></tr>
  <tr><th>Source</th><td>Cornerstone IDV process.</td></tr>
  <tr><th>Data Type</th><td>String / URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Title Evidence (010)*

<table>
  <tr><th>Attribute</th><td><code>title_evidence</code></td></tr>
  <tr><th>Description</th><td>UUID referencing land title evidence, author, and verification date.</td></tr>
  <tr><th>Source</th><td>Land Title Registry checks.</td></tr>
  <tr><th>Data Type</th><td>String / URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

## 5. Issuance Rules

### 5.1 When Credential is Issued

The Home Credential is issued when:
1. Holder possesses a valid Cornerstone ID
2. Identity verification matches land title registry records (name, address)
3. Title verification confirms property ownership via PID
4. Holder has generated a DID in their Cornerstone Wallet

### 5.2 Credential Binding

The credential is bound to:
- A DID generated by the user's wallet (stored in `credentialSubject.id`)
- A specific property (via `pid` and `property_address`)
- The holder's Cornerstone ID (via `cornerstone_id` reference)

## 6. Refresh & Expiration

### 6.1 Expiration Period

Expiration period is determined by operational policy.

### 6.2 Refresh Triggers

- Expiration date approaches
- Property ownership changes
- Identity information changes
- Holder requests refresh

### 6.3 Refresh Process

Refresh requires revocation of previous credential and issuance of new credential with updated data.

## 7. Revocation Policy

### 7.1 Revocation Triggers

1. Property sold or ownership updated
2. Cornerstone ID revoked or expired
3. Identity or title evidence expires
4. Fraud or errors detected
5. Holder requests re-issuance
6. Regulatory or legal request

### 7.2 Cascade Revocation

When a Home Credential is revoked:
- All PAACs for that specific property are revoked (managed operationally by the platform)
- Other properties owned by the same homeowner are unaffected

### 7.3 Revocation Method

Credential status managed via **W3C Bitstring Status List v1.1** with both revocation and suspension bitstrings.

## 8. Validation Rules

### 8.1 Required Fields

**credentialSubject:**
- `cornerstone_id`
- `pid`
- `property_address`
- `jurisdiction`
- `purchase_price`
- `purchase_date`
- `year_built`
- `neighbourhood`
- `identity_evidence`
- `title_evidence`

**Envelope:**
- `issuer` (must be Cornerstone Network DID)
- `validFrom`
- `validUntil`
- `credentialSchema`
- `credentialStatus`

**Evidence:**
- At least one `evidence` object

## 9. Policy Integration

### 9.1 Capabilities Granted

1. **Property Trust Network Management**: Issue PAACs to advisors and service providers; manage trust network membership
2. **Property Data Sharing**: Share verified property ownership with lenders, insurers, financial institutions
3. **Homeowner Platform Access**: Access homeowner-specific features in Cornerstone applications

### 9.2 Related Credentials

- **References**: Cornerstone ID (via `cornerstone_id`)
- **Referenced by**: PAAC (via `property_id` and `pid`)

## 10. Schema Definition (High-Level)

```json
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://trustinfrastructure.com/cornerstone/contexts/home-credential-v1.json"
  ],
  "type": ["VerifiableCredential", "HomeCredential"],
  "issuer": "did:web:trustinfrastructure.com:cornerstone",
  "validFrom": "2026-01-15T14:32:00Z",
  "validUntil": "2031-01-15T14:32:00Z",
  "credentialSubject": {
    "id": "did:example:user123",
    "cornerstone_id": "did:example:user123",
    "pid": "027-263-975",
    "property_address": {
      "street_address": "1234 W 10th Ave",
      "locality": "Vancouver",
      "region": "BC",
      "postal_code": "V6H 1J9",
      "country": "CA"
    },
    "jurisdiction": "BC",
    "purchase_price": 1250000,
    "purchase_date": "2018-05-14",
    "year_built": 1987,
    "neighbourhood": "Kitsilano",
    "identity_evidence": "urn:uuid:a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "title_evidence": "urn:uuid:b2c3d4e5-f6a7-8901-bcde-f12345678901"
  },
  "credentialStatus": [
    {
      "id": "https://trustinfrastructure.com/cornerstone/status/revocation#23456",
      "type": "BitstringStatusListEntry",
      "statusPurpose": "revocation",
      "statusListIndex": "23456",
      "statusListCredential": "https://trustinfrastructure.com/cornerstone/status/revocation"
    },
    {
      "id": "https://trustinfrastructure.com/cornerstone/status/suspension#23456",
      "type": "BitstringStatusListEntry",
      "statusPurpose": "suspension",
      "statusListIndex": "23456",
      "statusListCredential": "https://trustinfrastructure.com/cornerstone/status/suspension"
    }
  ],
  "evidence": [
    {
      "type": "IdentityProofing",
      "method": "InteracBankVerification",
      "verificationDate": "2026-01-15T14:32:00Z",
      "matchFields": ["given_name", "family_name", "birthdate"],
      "recordLocator": "urn:uuid:a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "verifier": "Interac Corp."
    },
    {
      "type": "TitleVerification",
      "method": "LandTitleRegistryCheck",
      "verificationDate": "2026-01-15T14:35:00Z",
      "matchFields": ["pid", "owner_name", "property_address"],
      "recordLocator": "urn:uuid:b2c3d4e5-f6a7-8901-bcde-f12345678901",
      "verifier": "Cornerstone Network"
    }
  ],
  "credentialSchema": {
    "id": "https://trustinfrastructure.com/cornerstone/schemas/home-credential.json",
    "type": "JsonSchema"
  }
}
```

## 11. References

### 11.1 Industry References

- **vLEI (GLEIF)** — Chained credential model where credentials reference parent credentials rather than duplicating attributes. [gleif.org/vlei](https://www.gleif.org/en/organizational-identity/introducing-the-verifiable-lei-vlei)
- **W3C Verifiable Credentials Data Model 2.0** — [w3.org/TR/vc-data-model-2.0](https://www.w3.org/TR/vc-data-model-2.0/)
- **W3C Bitstring Status List v1.1** — [w3c.github.io/vc-bitstring-status-list](https://w3c.github.io/vc-bitstring-status-list/)

---

**Document Control**

- **Owner**: Cornerstone Network
- **Governance Body**: Cornerstone Network
- **Review Cycle**: Annual or upon breaking schema changes
- **Schema Registry**: `https://trustinfrastructure.com/cornerstone/schemas/` *(exact URL TBD)*
