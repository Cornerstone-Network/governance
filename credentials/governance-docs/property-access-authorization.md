# Property Access Authorization Credential (PAAC) – Credential Governance

## 1. About this Document

This document describes the **Property Access Authorization Credential (PAAC)** verifiable credential to help potential verifiers determine whether it is suitable for their needs. The intended audience includes real estate professionals, lenders, insurers, financial service providers, trust network members, and any relying parties requiring authorized access to homeowner portfolio data.

The PAAC is issued by the **Cornerstone Network** on behalf of homeowners to members of their property-specific trust network — professionals, advisors, service providers, family members, and trusted individuals. It represents **authorization to access specific portfolio data for a specific property** as defined by the homeowner.

**External terminology note**: This credential is referred to as "Letter of Authorization" in user-facing applications, while "Property Access Authorization Credential (PAAC)" is the internal technical term.

### 1.1 Version History

| Ver.      | Date        | Notes                               | Author(s) |
|-----------|-------------|-------------------------------------|-------------|
| **0.9**   | 26-Feb-2026 | Replaced `property_address` with `pid` for atomicity; updated signing model to Cornerstone Network on behalf of homeowners; updated schema hosting; added design rationale | Mathieu Glaude |
| **0.1**   | TBA         | Initial release                      | Mathieu Glaude |

## 2. Credential Overview

The PAAC is a verifiable credential (VC) that grants selective access to property portfolio data for trust network members. It enables the holder to access specific portfolio information while maintaining granular property-level and data-level control over what is shared and for what purpose.

- **Issued on behalf of homeowners** to trust network members for a specific property
- **Property-specific scope**: Each Home Credential (property) has its own trust network; separate PAACs issued per property
- **Defines data sharing scope**: Specifies which portfolio data elements can be accessed
- **Purpose-bound authorization**: Documents the intent and purpose of data sharing
- **Access level control**: Read-only, operational, advisory, or transactional access
- **Time-bound validity**: Can include expiration dates or remain valid until revoked
- **Revocable at any time**

|              |                                                                 |
|--------------|-----------------------------------------------------------------|
| **Credential:** | Property Access Authorization Credential (PAAC) / "Letter of Authorization" |
| **Schema:**     | PAAC v0.9                                                   |
| **Issuer:**     | Cornerstone Network (on behalf of homeowners)               |
| **Issuer DID:** | TBD (e.g., `did:web:trustinfrastructure.com:cornerstone`)   |
| **Format:**     | W3C Verifiable Credentials (JSON-LD)                        |

### 2.1 Attribute Summary (15 Attributes)

| **#** | **Name**            | **Attribute**               | **Data Type**            | **Required** | **Notes** |
|-------|---------------------|-----------------------------|--------------------------|--------------|-----------|
| 001   | Authorization ID    | `authorization_id`          | String (UUID)            | Yes          | Platform-generated unique identifier. |
| 002   | Homeowner ID        | `homeowner_id`              | String (UUID)            | Yes          | Links to issuer's Cornerstone ID. |
| 003   | Homeowner DID       | `homeowner_did`             | String (DID)             | Yes          | From homeowner's Cornerstone Wallet. |
| 004   | Trust Network Member ID | `tnm_id`               | String (UUID)            | Yes          | Links to recipient's Cornerstone ID. |
| 005   | Trust Network Member DID | `tnm_did`              | String (DID)             | Yes          | From recipient's Cornerstone Wallet. |
| 006   | Property ID         | `property_id`               | String (UUID)            | Yes          | Links to specific property/portfolio. |
| 007   | PID                 | `pid`                       | String                   | Yes          | Provincial land title identifier for property resolution. |
| 008   | Data Scope          | `data_scope`                | JSON array (enum strings)| Yes          | Data categories the recipient can access. |
| 009   | Authorization Purpose | `authorization_purpose`   | String                   | Yes          | Intent of data sharing. |
| 010   | Access Level        | `access_level`              | String (enum)            | Yes          | `READ_ONLY`, `OPERATIONAL`, `ADVISORY`, `TRANSACTIONAL`. |
| 011   | Start Date          | `start_date`                | String (ISO 8601)        | Yes          | Authorization validity commencement. |
| 012   | Expiration Date     | `expiration_date`           | String (ISO 8601) or null | No          | If omitted, valid until revoked. |
| 013   | Granted Date        | `granted_date`              | String (ISO 8601)        | Yes          | Platform-generated at issuance. |
| 014   | Authorization Evidence | `authorization_evidence` | String / URI             | Yes          | References homeowner action log. |
| 015   | Relationship Category | `relationship_category`   | String (enum)            | Yes          | Category of TNM's relationship to homeowner. |

### 2.2 Design Rationale

**Attribute decisions in this version:**

- **Replaced `property_address` (JSON object) with `pid` (String).** The full property address already lives in the Home Credential. The PAAC uses `property_id` (platform internal UUID) and `pid` (provincial land title registry identifier) to resolve the link to the property. This avoids triple-duplication of address data across Cornerstone ID (`postal_address`), Home Credential (`property_address`), and PAAC.

- **Signing model: Cornerstone Network signs on behalf of homeowners.** For the initial release, homeowner onboarding happens through professionals, and PAACs are auto-issued when a professional onboards a homeowner. The homeowner's consent is captured through the platform flow and recorded in the `authorization_evidence` reference. The `homeowner_id` and `homeowner_did` identify the authorizing homeowner. Granular homeowner controls for modifying/revoking PAACs are planned for a future release.

- **Access levels defined but enforcement needs validation.** The four access levels and ten `data_scope` values need to be validated against the actual platform implementation before shipping. The dev team should ensure enforcement logic matches these definitions.

### 2.3 Removed Attributes (v0.9)

| Attribute | Reason for Removal |
|-----------|-------------------|
| `property_address` (JSON object) | Replaced by `pid` (String). The full address already lives in the Home Credential. Using `property_id` + `pid` is sufficient for the platform to resolve the property link, avoiding data duplication. |

## 3. Credential Details

### 3.1 Issuer

The PAAC is issued by the **Cornerstone Network** on behalf of homeowners. For the initial release, PAACs are auto-issued during homeowner onboarding by professionals.

### 3.2 Data Scope Values

The `data_scope` attribute accepts the following enum values:

| Value | Description |
|-------|-------------|
| `identity` | Homeowner identity information |
| `ownership` | Property ownership details |
| `property_details` | Physical property characteristics |
| `equity` | Equity position (market value, mortgage balance) |
| `costs` | Property costs (taxes, maintenance, utilities) |
| `insurance` | Insurance policy information |
| `mortgage` | Mortgage details and terms |
| `valuations` | Property valuations and appraisals |
| `documents` | Property documents (title, agreements) |
| `full_portfolio` | Complete portfolio access (all categories) |

### 3.3 Access Levels

| Level | Description |
|-------|-------------|
| `READ_ONLY` | View data only. |
| `OPERATIONAL` | View + operational actions (e.g., property manager updating maintenance logs). |
| `ADVISORY` | View + advisory actions (e.g., REP providing market insights). |
| `TRANSACTIONAL` | View + transactional actions (e.g., authorized to initiate refinance on homeowner's behalf). |

### 3.4 Relationship Categories

Valid values for `relationship_category`: `realtor`, `mortgage_broker`, `family_member`, `accountant`, `lawyer`, `insurance_agent`, `property_manager`, `contractor`, `financial_advisor`, `other`.

### 3.5 Credential Status

Credential status is managed via the **W3C Bitstring Status List v1.1**, supporting both `revocation` and `suspension`.

### 3.6 Revocation

A PAAC will be revoked in cases such as:
1. Homeowner explicitly revokes authorization (via platform)
2. Credential reaches `expiration_date` (automatic)
3. Trust network relationship ends
4. Property sold or Home Credential revoked
5. Homeowner's Cornerstone ID revoked
6. Recipient's Cornerstone ID revoked or expired
7. Security incident or unauthorized use detected
8. Homeowner account closed or deactivated
9. Legal or regulatory request

**Cascade Revocation** (managed operationally by platform):
- Homeowner's Cornerstone ID revoked → all PAACs issued on behalf of that homeowner are revoked
- Homeowner's Home Credential for specific property revoked → all PAACs for that property are revoked
- Recipient's Cornerstone ID revoked → all PAACs held by that recipient are revoked

**Revocation is immediate** — no grace period. Access terminates immediately upon revocation.

## 4. Credential Definition

### 4.1 Credential Schema

- **Schema ID (URI):** `https://trustinfrastructure.com/cornerstone/schemas/property-access-authorization.json` *(exact URL TBD)*
- **Contexts:**
  - `https://www.w3.org/ns/credentials/v2`
  - `https://trustinfrastructure.com/cornerstone/contexts/property-access-authorization-v0.9.json` *(exact URL TBD)*

### 4.2 Subject of the Credential

The subject is the **trust network member (recipient)**, bound to their Cornerstone ID, with association to a specific property via `property_id` and `pid`.

## 5. Issuance Rules

The PAAC is issued when ALL of the following are satisfied:
1. Homeowner holds valid Cornerstone ID
2. Homeowner holds valid Home Credential for the specific property referenced
3. Recipient holds valid Cornerstone ID
4. Authorization scope, purpose, access level, and validity period are defined
5. Platform validates homeowner authority and recipient identity

**For the initial release**: PAACs are auto-issued when a professional onboards a homeowner. The homeowner's consent is captured through the platform onboarding flow.

## 6. Refresh & Expiration

**Expiration patterns:**
- **Short-term service provider access**: 30-90 days (insurance quote, mortgage application)
- **Ongoing professional advisor access**: 1-3 years (REP relationship, financial advisor)
- **Family trust network access**: No expiration (ongoing family monitoring)

**Refresh** follows revoke-and-reissue pattern (no in-place updates).

## 7. Validation Rules

### 7.1 Required Fields

**credentialSubject:**
- `authorization_id`, `homeowner_id`, `homeowner_did`, `tnm_id`, `tnm_did`, `property_id`, `pid`, `data_scope`, `authorization_purpose`, `access_level`, `start_date`, `granted_date`, `authorization_evidence`, `relationship_category`

**Envelope:**
- `issuer` (must be Cornerstone Network DID)
- `validFrom`
- `credentialSchema`
- `credentialStatus`

### 7.2 Forbidden Fields

- Portfolio data contents (equity amounts, mortgage balances, property valuations, insurance policy details)
- Sensitive personal information (SINs, tax IDs, banking info, credit scores)
- Predicates or derived fields
- Assurance level indicators

## 8. Policy Integration

### 8.1 Capabilities Granted

- Property-specific portfolio data access per `data_scope`
- Purpose-bound access per `authorization_purpose`
- Access level enforcement per `access_level`
- Time-bound access per `start_date` / `expiration_date`

### 8.2 Out of Scope

This credential does NOT:
- Grant ownership rights or control over the property
- Include sensitive financial data directly (credentials enable access, not contain data)
- Authorize changes to portfolio data (unless explicitly scoped as TRANSACTIONAL)

## 9. Schema Definition (High-Level)

```json
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://trustinfrastructure.com/cornerstone/contexts/property-access-authorization-v0.9.json"
  ],
  "type": ["VerifiableCredential", "PropertyAccessAuthorizationCredential"],
  "issuer": "did:web:trustinfrastructure.com:cornerstone",
  "validFrom": "2026-03-25T14:32:00Z",
  "credentialSubject": {
    "id": "did:example:tnm456",
    "authorization_id": "d4e5f6a7-b8c9-0123-def0-123456789012",
    "homeowner_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "homeowner_did": "did:example:homeowner123",
    "tnm_id": "e5f6a7b8-c9d0-1234-ef01-234567890123",
    "tnm_did": "did:example:tnm456",
    "property_id": "f6a7b8c9-d0e1-2345-f012-345678901234",
    "pid": "027-263-975",
    "data_scope": ["identity", "ownership", "equity", "insurance"],
    "authorization_purpose": "Mortgage refinance consultation",
    "access_level": "READ_ONLY",
    "relationship_category": "mortgage_broker",
    "start_date": "2026-04-01T00:00:00Z",
    "expiration_date": "2027-04-01T00:00:00Z",
    "granted_date": "2026-03-25T14:32:00Z",
    "authorization_evidence": "urn:uuid:d4e5f6a7-b8c9-0123-def0-123456789012"
  },
  "credentialStatus": [
    {
      "id": "https://trustinfrastructure.com/cornerstone/status/revocation#67890",
      "type": "BitstringStatusListEntry",
      "statusPurpose": "revocation",
      "statusListIndex": "67890",
      "statusListCredential": "https://trustinfrastructure.com/cornerstone/status/revocation"
    }
  ],
  "evidence": [
    {
      "type": "AuthorizationAction",
      "method": "PlatformAuthorizationFlow",
      "verificationDate": "2026-03-25T14:32:00Z",
      "matchFields": ["homeowner_authority", "property_ownership", "recipient_identity"],
      "recordLocator": "urn:uuid:d4e5f6a7-b8c9-0123-def0-123456789012",
      "verifier": "Cornerstone Network"
    }
  ],
  "credentialSchema": {
    "id": "https://trustinfrastructure.com/cornerstone/schemas/property-access-authorization.json",
    "type": "JsonSchema"
  }
}
```

## 10. References

### 10.1 Industry References

- **W3C Verifiable Credentials Data Model 2.0** — [w3.org/TR/vc-data-model-2.0](https://www.w3.org/TR/vc-data-model-2.0/)
- **W3C Bitstring Status List v1.1** — [w3c.github.io/vc-bitstring-status-list](https://w3c.github.io/vc-bitstring-status-list/)

---

**Document Control**

- **Owner**: Cornerstone Network
- **Governance Body**: Cornerstone Network
- **Review Cycle**: Annual or upon breaking schema changes
- **Schema Registry**: `https://trustinfrastructure.com/cornerstone/schemas/` *(exact URL TBD)*
