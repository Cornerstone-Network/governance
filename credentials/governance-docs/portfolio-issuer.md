# Portfolio Issuer Credential – Credential Governance

## 1. About this Document

This document describes the **Portfolio Issuer Credential** verifiable credential to help potential verifiers determine whether it is suitable for their needs. The intended audience includes Network Partners, Cornerstone Network governance bodies, real estate professionals, mortgage brokers, insurance agents, and any relying parties requiring verified Portfolio Issuer status.

The Portfolio Issuer Credential is issued by the **Cornerstone Network** to professionals who are authorized to create homeowner portfolios, issue credentials to homeowners, and approve homeowner onboarding to the Cornerstone network.

### 1.1 Version History

| Ver.      | Date        | Notes                               | Author(s) |
|-----------|-------------|-------------------------------------|-------------|
| **2.0**   | 26-Feb-2026 | JSON-LD format finalization; removed `canIssuePortfolios` (redundant), `professional_credential_ref` (no hard dependency), and `issuer_type` (single type for v1); added `cornerstone_id` reference; updated schema hosting; added design rationale and industry references | Mathieu Glaude |
| **1.0**   | TBA         | Initial release                      | Mathieu Glaude |

## 2. Credential Overview

The Portfolio Issuer Credential is a verifiable credential (VC) issued to professionals who have been granted authority by a Network Partner to create portfolios on behalf of homeowner clients. It enables the holder to prove their Portfolio Issuer status while sharing only the minimum data required.

This credential represents the authority to:
- Approve homeowner onboarding to the Cornerstone network
- Issue Cornerstone ID credentials (identity verification)
- Issue Home Credentials (property ownership verification)
- Initiate data integration from authoritative sources
- Earn referral fees on downstream transactions
- Receive default portfolio branding rights

Each issuance is backed by:
- Network Partner attestation (approval by NP or Cornerstone Network)
- Reference to holder's verified Cornerstone ID

The credential is issued directly into the **Cornerstone Wallet**.

|              |                                                                 |
|--------------|-----------------------------------------------------------------|
| **Credential:** | Portfolio Issuer Credential                                 |
| **Schema:**     | Portfolio Issuer v2.0                                       |
| **Issuer:**     | Cornerstone Network                                         |
| **Issuer DID:** | TBD (e.g., `did:web:trustinfrastructure.com:cornerstone`)   |
| **Format:**     | W3C Verifiable Credentials (JSON-LD)                        |

### 2.1 Attribute Summary (8 Attributes)

| **#** | **Name**            | **Attribute**               | **Data Type**       | **Required** | **Notes** |
|-------|---------------------|-----------------------------|---------------------|--------------|-----------|
| 001   | Cornerstone ID Reference | `cornerstone_id`       | DID/URI             | Yes          | Reference to holder's Cornerstone ID. |
| 002   | Portfolio Issuer ID | `portfolio_issuer_id`       | String (UUID)       | Yes          | Platform-generated unique identifier. |
| 003   | Network Partner ID  | `network_partner_id`        | String (UUID)       | Yes          | Identifier of granting Network Partner. |
| 004   | Network Partner Name | `network_partner_name`     | String              | Yes          | Human-readable NP name (e.g., "Sutton Group"). |
| 005   | Granted Date        | `granted_date`              | String (ISO 8601)   | Yes          | When capability was granted. |
| 006   | Granting Authority  | `granting_authority`        | String              | Yes          | NP name or "Cornerstone Network". |
| 007   | Portfolio Issuer Evidence | `portfolio_issuer_evidence` | String / URI    | Yes          | Reference to attestation evidence. |
| 008   | Scope               | `scope`                     | JSON object         | No           | Geographic or organizational restrictions. |

### 2.2 Design Rationale

**Attribute decisions in this version:**

- **Removed `canIssuePortfolios`** (was always `true`). A boolean that is always `true` for every valid credential carries zero information. The credential's existence IS the authorization — a capability credential should not re-assert its own purpose as an attribute.

- **Removed `professional_credential_ref`**. No hard dependency between credentials. The Portfolio Issuer, Professional, and Accreditation credentials are independent. In practice, a Portfolio Issuer is assumed to be a professional, but this is a business-level assumption, not a credential-level enforcement. Removing this reference makes the credentials truly independent.

- **Removed `issuer_type`** (ORGANIZATIONAL vs DELEGATE). Single type for v1 to keep it simple. If the distinction becomes important in the future, it should be modeled as separate credential types (following the vLEI pattern of OOR vs ECR) rather than a type field within a single credential.

- **Added `cornerstone_id` reference**. For consistency with all other credentials. Every non-foundation credential references the holder's Cornerstone ID.

- **Renamed `npId` → `network_partner_id` and `np_name` → `network_partner_name`**. For naming consistency across the credential ecosystem.

**Industry references:**

- **vLEI (GLEIF)**: Models role distinctions as separate credential types (Official Organizational Role vs Engagement Context Role) rather than a type field within one credential. If ORGANIZATIONAL vs DELEGATE distinction becomes needed, this is the pattern to follow.

### 2.3 Removed Attributes (v2.0)

| Attribute | Reason for Removal |
|-----------|-------------------|
| `canIssuePortfolios` (Boolean, always `true`) | Redundant. The credential's existence is the authorization. A capability credential that always has a capability flag set to `true` adds no information. |
| `professional_credential_ref` (String UUID) | No hard dependency between credentials. Portfolio Issuer is independent. Business-level assumption that portfolio issuers are professionals, not credential-level enforcement. |
| `issuer_type` (String enum: ORGANIZATIONAL/DELEGATE) | Single type for v1. Future distinction should use separate credential types per vLEI pattern, not an attribute within one credential. |

## 3. Credential Details

### 3.1 Issuer

The Portfolio Issuer Credential is issued by the **Cornerstone Network**. Each credential is issued only after Network Partner attestation and holder identity verification.

### 3.2 Issuer Data Source

The data comes from:
- **Network Partner attestation**: NP approval records confirming Portfolio Issuer authorization
- **Cornerstone ID verification**: Holder's identity already established

### 3.3 Credential Status

Credential status is managed via the **W3C Bitstring Status List v1.1**, supporting both `revocation` and `suspension`.

### 3.4 Revocation

A Portfolio Issuer Credential will be revoked in cases such as:
1. Network Partner terminates Portfolio Issuer authorization
2. Organization affiliation ends
3. Cornerstone ID revoked
4. Fraud or misuse detected
5. Regulatory or legal request
6. Network Partner membership in Cornerstone Network ends
7. Holder requests revocation

**Post-Revocation**: Existing portfolios created by this Portfolio Issuer remain valid. New portfolio creation is blocked until credential is re-issued.

## 4. Credential Definition

### 4.1 Credential Schema

- **Schema ID (URI):** `https://trustinfrastructure.com/cornerstone/schemas/portfolio-issuer.json` *(exact URL TBD)*
- **Contexts:**
  - `https://www.w3.org/ns/credentials/v2`
  - `https://trustinfrastructure.com/cornerstone/contexts/portfolio-issuer-v2.json` *(exact URL TBD)*

### 4.2 Attributes

#### 4.2.1 Required Attributes

*Cornerstone ID Reference (001)*

<table>
  <tr><th>Attribute</th><td><code>cornerstone_id</code></td></tr>
  <tr><th>Description</th><td>Reference to the holder's verified Cornerstone ID credential.</td></tr>
  <tr><th>Data Type</th><td>DID/URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Portfolio Issuer ID (002)*

<table>
  <tr><th>Attribute</th><td><code>portfolio_issuer_id</code></td></tr>
  <tr><th>Description</th><td>Platform-generated unique identifier for this Portfolio Issuer.</td></tr>
  <tr><th>Data Type</th><td>String (UUID)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Network Partner ID (003)*

<table>
  <tr><th>Attribute</th><td><code>network_partner_id</code></td></tr>
  <tr><th>Description</th><td>Identifier of the Network Partner granting this credential.</td></tr>
  <tr><th>Data Type</th><td>String (UUID)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Network Partner Name (004)*

<table>
  <tr><th>Attribute</th><td><code>network_partner_name</code></td></tr>
  <tr><th>Description</th><td>Human-readable name of the Network Partner.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>Sutton Group</code>, <code>Dominion Lending</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Granted Date (005)*

<table>
  <tr><th>Attribute</th><td><code>granted_date</code></td></tr>
  <tr><th>Description</th><td>ISO 8601 timestamp of when Portfolio Issuer capability was granted.</td></tr>
  <tr><th>Data Type</th><td>String (ISO 8601)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Granting Authority (006)*

<table>
  <tr><th>Attribute</th><td><code>granting_authority</code></td></tr>
  <tr><th>Description</th><td>Entity that granted Portfolio Issuer authority.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>Sutton Group</code>, <code>Cornerstone Network</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Portfolio Issuer Evidence (007)*

<table>
  <tr><th>Attribute</th><td><code>portfolio_issuer_evidence</code></td></tr>
  <tr><th>Description</th><td>UUID referencing Network Partner attestation source(s) and authorization date.</td></tr>
  <tr><th>Data Type</th><td>String (UUID) or URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

#### 4.2.2 Optional Attributes

*Scope (008)*

<table>
  <tr><th>Attribute</th><td><code>scope</code></td></tr>
  <tr><th>Description</th><td>Geographic or organizational scope where Portfolio Issuer authority applies.</td></tr>
  <tr><th>Data Type</th><td>JSON object containing: <code>regions</code> (array), <code>offices</code> (array), <code>service_areas</code> (array), <code>property_types</code> (array)</td></tr>
  <tr><th>Examples</th><td><code>{"regions": ["British Columbia", "Ontario"], "offices": ["Vancouver Downtown"]}</code></td></tr>
  <tr><th>Required</th><td>No — only if NP applies scope restrictions</td></tr>
</table>

## 5. Expiration & Refresh

**Expiration Period:** 1-3 years after issuance (determined by Network Partner policy or Cornerstone Network governance).

**Refresh** follows revoke-and-reissue pattern.

## 6. Validation Rules

**Required credentialSubject fields:**
- `cornerstone_id`, `portfolio_issuer_id`, `network_partner_id`, `network_partner_name`, `granted_date`, `granting_authority`, `portfolio_issuer_evidence`

## 7. Policy Integration

### 7.1 Capabilities Granted

- **Portfolio creation authority**: Approve homeowner onboarding, issue Cornerstone IDs and Home Credentials
- **Data integration authority**: Initiate portfolio population from authoritative data sources
- **Branding rights**: Portfolio displays Network Partner branding
- **Referral fee rights**: Earn referral fees when homeowner clients transact with service providers

### 7.2 Related Credentials

- **References**: Cornerstone ID (via `cornerstone_id`)
- **Independent of**: Professional Credential, Accreditation Credential
- **Can issue to others**: Cornerstone ID (to homeowner clients), Home Credential (to homeowner clients)

## 8. Schema Definition (High-Level)

```json
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://trustinfrastructure.com/cornerstone/contexts/portfolio-issuer-v2.json"
  ],
  "type": ["VerifiableCredential", "PortfolioIssuerCredential"],
  "issuer": "did:web:trustinfrastructure.com:cornerstone",
  "validFrom": "2026-03-15T10:30:00Z",
  "validUntil": "2029-03-15T10:30:00Z",
  "credentialSubject": {
    "id": "did:example:professional123",
    "cornerstone_id": "did:example:professional123",
    "portfolio_issuer_id": "550e8400-e29b-41d4-a716-446655440000",
    "network_partner_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "network_partner_name": "Sutton Group",
    "granted_date": "2026-03-15T10:30:00Z",
    "granting_authority": "Sutton Group",
    "portfolio_issuer_evidence": "urn:uuid:c3d4e5f6-a7b8-9012-cdef-123456789012",
    "scope": {
      "regions": ["British Columbia", "Ontario"],
      "offices": ["Vancouver Downtown", "Toronto Midtown"]
    }
  },
  "credentialStatus": [
    {
      "id": "https://trustinfrastructure.com/cornerstone/status/revocation#56789",
      "type": "BitstringStatusListEntry",
      "statusPurpose": "revocation",
      "statusListIndex": "56789",
      "statusListCredential": "https://trustinfrastructure.com/cornerstone/status/revocation"
    }
  ],
  "credentialSchema": {
    "id": "https://trustinfrastructure.com/cornerstone/schemas/portfolio-issuer.json",
    "type": "JsonSchema"
  }
}
```

## 9. References

### 9.1 Industry References

- **vLEI (GLEIF)** — Models role distinctions (OOR vs ECR) as separate credential types rather than a type field. [gleif.org/vlei](https://www.gleif.org/en/organizational-identity/introducing-the-verifiable-lei-vlei)
- **W3C Verifiable Credentials Data Model 2.0** — [w3.org/TR/vc-data-model-2.0](https://www.w3.org/TR/vc-data-model-2.0/)
- **W3C Bitstring Status List v1.1** — [w3c.github.io/vc-bitstring-status-list](https://w3c.github.io/vc-bitstring-status-list/)

---

**Document Control**

- **Owner**: Cornerstone Network
- **Governance Body**: Cornerstone Network
- **Review Cycle**: Annual or upon breaking schema changes
- **Schema Registry**: `https://trustinfrastructure.com/cornerstone/schemas/` *(exact URL TBD)*
