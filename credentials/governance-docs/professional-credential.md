# Professional Credential – Credential Governance

## 1. About this Document

This document describes the **Professional Credential** verifiable credential to help potential verifiers determine whether it is suitable for their needs. The intended audience includes entities that need to confirm a professional's affiliation with an organization.

The Professional Credential is issued by the **Cornerstone Network** (as a service on behalf of Network Partners / employer organizations) and represents authoritative proof of a professional's affiliation with that organization.

### 1.1 Version History

| Ver.      | Date        | Notes                               | Author(s) |
|-----------|-------------|-------------------------------------|-------------|
| **1.0**   | 26-Feb-2026 | JSON-LD format finalization; removed `status` field (revocation replaces status tracking); updated issuer to Cornerstone Network; updated schema hosting; added design rationale and industry references | Mathieu Glaude |
| **0.2**   | 4-Dec-2025  | Refocused on employment/affiliation (regulatory claims moved to Accreditation Credential) | Mathieu Glaude |
| **0.1**   | 3-Sep-2025  | Initial draft                        | Mathieu Glaude |

## 2. Credential Overview

The Professional Credential is a verifiable credential (VC) issued to individuals who are affiliated with an employer organization as employees, contractors, or delegates. This credential focuses exclusively on **organizational affiliation** and does not include regulatory licensing information.

The credential references the holder's **Cornerstone ID** for identity binding, rather than duplicating identity claims.

|              |                                                                 |
|--------------|-----------------------------------------------------------------|
| **Credential:** | Professional Credential                                     |
| **Schema:**     | Professional Credential v1.0                                |
| **Issuer:**     | Cornerstone Network (on behalf of Network Partners)         |
| **Issuer DID:** | TBD (e.g., `did:web:trustinfrastructure.com:cornerstone`)   |
| **Format:**     | W3C Verifiable Credentials (JSON-LD)                        |

### 2.1 Attribute Summary (7 Attributes)

| **#** | **Name**            | **Attribute**          | **Data Type**       | **Required** | **Notes** |
|-------|---------------------|------------------------|---------------------|--------------|-----------|
| 001   | Cornerstone ID Reference | `cornerstone_id` | DID/URI             | Yes          | Reference to holder's Cornerstone ID. |
| 002   | Employer Name       | `employer_name`        | String              | Yes          | Organization legal name. |
| 003   | Employer DID        | `employer_did`         | DID                 | Yes          | Organization's decentralized identifier. |
| 004   | Role/Title          | `role_title`           | String              | Yes          | Job title or role within organization. |
| 005   | Affiliation Type    | `affiliation_type`     | String (enum)       | Yes          | `employee`, `contractor`, `delegate`. |
| 006   | Start Date          | `start_date`           | String (YYYY-MM-DD) | Yes          | When affiliation began. |
| 007   | Team/Office         | `team_office`          | String              | No           | Department, team, or office location. |

### 2.2 Design Rationale

**Attribute decisions in this version:**

- **Removed `status` field.** A credential with `status: terminated` is semantically contradictory — why would a valid, signed credential assert that the relationship it attests to no longer exists? When an affiliation ends, the credential is revoked via the W3C Bitstring Status List. A revoked credential can still be presented by the holder, and the verifier sees the revocation status through the credential status mechanism. This is the standard pattern across all production VC ecosystems studied.

- **Issued by Cornerstone Network as a service.** While conceptually this is an employer attestation, Cornerstone Network issues these as a service on behalf of Network Partners for v1. Network partners provide employment/affiliation data out of band. The employer registration process is managed by Cornerstone. No employer-facing issuance portal is needed for v1.

- **Independent from other credentials.** The Professional Credential is independent from the Accreditation Credential and Portfolio Issuer Credential. A professional may hold only a Professional Credential (employed but not licensed), only an Accreditation Credential (licensed but not employed), or both. No hard dependencies are enforced between them.

**Industry references:**

- **vLEI Engagement Context Role (ECR)**: The ECR credential follows the same pattern — an organization attests to an individual's role, and status changes are handled by revoking and reissuing the credential. The ECR also uses free-form role strings rather than enforcing an enum for role titles.

### 2.3 Removed Attributes (v1.0)

| Attribute | Reason for Removal |
|-----------|-------------------|
| `status` (String: active/inactive/terminated) | Mutable status embedded in an immutable signed credential is an anti-pattern. When affiliation ends, the credential is revoked. The W3C Bitstring Status List supports both revocation (permanent) and suspension (reversible), covering all status change scenarios. |

## 3. Credential Details

### 3.1 Issuer

The Professional Credential is issued by the **Cornerstone Network**, acting as credential authority on behalf of Network Partners. Each credential is issued only after the employer organization provides affiliation data and the holder's Cornerstone ID is verified.

**Operational note**: Network partners provide employment/affiliation data to Cornerstone out of band. The employer registration process is managed by Cornerstone. No employer-facing issuance portal is required for v1.

### 3.2 Schema and Credential Definition Governance

The Professional Credential schema is managed by the Cornerstone Network and registered on the Cornerstone Trust Registry. While the schema is standardized, each credential identifies the employer via `employer_name` and `employer_did`.

### 3.3 Issuer Data Source

The data comes from the employer organization's HR or personnel systems, provided to Cornerstone:

- **Affiliation verification**: The organization attests that the individual is affiliated with them
- **Role assignment**: The organization assigns the professional's role/title
- **Employment records**: Start date from HR systems

#### 3.3.1 Data Updates
- A credential reflects the state of employment at issuance.
- Affiliation changes (termination, role change) require revocation and re-issuance.
- Organizations are responsible for timely notification to Cornerstone when affiliation ends.

### 3.4 Assurance

The credential combines two assurance anchors:
- Verified Cornerstone ID (identity already established)
- Employer attestation of affiliation (provided to Cornerstone)

### 3.5 Credential Status

Credential status is managed via the **W3C Bitstring Status List v1.1**, supporting both `revocation` (permanent — affiliation ended) and `suspension` (reversible — temporary hold).

### 3.6 Revocation

A Professional Credential will be revoked in cases such as:
1. Employment/affiliation terminated
2. Role or affiliation type changed significantly (revoke and reissue)
3. Cornerstone ID revoked or invalidated
4. Fraud or errors detected
5. Holder requests re-issuance

Re-issuance involves Cornerstone issuing a new credential reflecting current status on behalf of the employer.

## 4. Credential Definition

### 4.1 Credential Schema

- **Schema ID (URI):** `https://trustinfrastructure.com/cornerstone/schemas/professional-credential.json` *(exact URL TBD)*
- **Schema Versioning:** Semantic versioning. Breaking changes produce a new major version.
- **Contexts:**
  - `https://www.w3.org/ns/credentials/v2`
  - `https://trustinfrastructure.com/cornerstone/contexts/professional-credential-v1.json` *(exact URL TBD)*

### 4.2 Subject of the Credential

The subject is the **individual holder**, bound to an **organizational affiliation** at issuance. Binding is confirmed by reference to verified Cornerstone ID and employer attestation.

### 4.3 Attributes

#### 4.3.1 Identity Reference

*Cornerstone ID Reference (001)*

<table>
  <tr><th>Attribute</th><td><code>cornerstone_id</code></td></tr>
  <tr><th>Description</th><td>Reference to the holder's verified Cornerstone ID credential.</td></tr>
  <tr><th>Source</th><td>Cornerstone ID issuance.</td></tr>
  <tr><th>Data Type</th><td>DID/URI</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

#### 4.3.2 Organization Attributes

*Employer Name (002)*

<table>
  <tr><th>Attribute</th><td><code>employer_name</code></td></tr>
  <tr><th>Description</th><td>Legal name of the employing organization.</td></tr>
  <tr><th>Source</th><td>Employer organization (via Cornerstone).</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>Sutton Group</code>, <code>Royal LePage</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Employer DID (003)*

<table>
  <tr><th>Attribute</th><td><code>employer_did</code></td></tr>
  <tr><th>Description</th><td>Decentralized identifier of the employing organization.</td></tr>
  <tr><th>Source</th><td>Trust Registry.</td></tr>
  <tr><th>Data Type</th><td>DID</td></tr>
  <tr><th>Examples</th><td><code>did:web:suttongroup.ca</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

#### 4.3.3 Affiliation Attributes

*Role/Title (004)*

<table>
  <tr><th>Attribute</th><td><code>role_title</code></td></tr>
  <tr><th>Description</th><td>The professional's job title or role within the organization.</td></tr>
  <tr><th>Source</th><td>Employer organization.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>Real Estate Agent</code>, <code>Mortgage Specialist</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Affiliation Type (005)*

<table>
  <tr><th>Attribute</th><td><code>affiliation_type</code></td></tr>
  <tr><th>Description</th><td>Type of relationship with the organization.</td></tr>
  <tr><th>Source</th><td>Employer organization.</td></tr>
  <tr><th>Data Type</th><td>String (enum)</td></tr>
  <tr><th>Values</th><td><code>employee</code>, <code>contractor</code>, <code>delegate</code></td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Start Date (006)*

<table>
  <tr><th>Attribute</th><td><code>start_date</code></td></tr>
  <tr><th>Description</th><td>Date when the affiliation with the organization began.</td></tr>
  <tr><th>Source</th><td>Employer organization.</td></tr>
  <tr><th>Data Type</th><td>String (YYYY-MM-DD)</td></tr>
  <tr><th>Required</th><td>Yes</td></tr>
</table>

*Team/Office (007)*

<table>
  <tr><th>Attribute</th><td><code>team_office</code></td></tr>
  <tr><th>Description</th><td>Department, team, or office location within the organization.</td></tr>
  <tr><th>Source</th><td>Employer organization.</td></tr>
  <tr><th>Data Type</th><td>String</td></tr>
  <tr><th>Examples</th><td><code>Vancouver Downtown Office</code></td></tr>
  <tr><th>Required</th><td>No</td></tr>
</table>

## 5. Validation Rules

### 5.1 Required Fields

**credentialSubject:**
- `cornerstone_id`
- `employer_name`
- `employer_did`
- `role_title`
- `affiliation_type`
- `start_date`

**Envelope:**
- `issuer` (must be Cornerstone Network DID)
- `validFrom`
- `credentialSchema`
- `credentialStatus`

## 6. Policy Integration

### 6.1 Related Credentials

- **References**: Cornerstone ID (via `cornerstone_id`)
- **Independent of**: Accreditation Credential, Portfolio Issuer Credential
- A professional may hold: only Professional Credential (employed, not licensed), only Accreditation Credential (licensed, not employed), or both

## 7. Schema Definition (High-Level)

```json
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://trustinfrastructure.com/cornerstone/contexts/professional-credential-v1.json"
  ],
  "type": ["VerifiableCredential", "ProfessionalCredential"],
  "issuer": "did:web:trustinfrastructure.com:cornerstone",
  "validFrom": "2026-03-01T10:00:00Z",
  "credentialSubject": {
    "id": "did:example:professional456",
    "cornerstone_id": "did:example:professional456",
    "employer_name": "Sutton Group",
    "employer_did": "did:web:suttongroup.ca",
    "role_title": "Real Estate Agent",
    "affiliation_type": "employee",
    "start_date": "2022-06-15",
    "team_office": "Vancouver Downtown Office"
  },
  "credentialStatus": [
    {
      "id": "https://trustinfrastructure.com/cornerstone/status/revocation#34567",
      "type": "BitstringStatusListEntry",
      "statusPurpose": "revocation",
      "statusListIndex": "34567",
      "statusListCredential": "https://trustinfrastructure.com/cornerstone/status/revocation"
    }
  ],
  "credentialSchema": {
    "id": "https://trustinfrastructure.com/cornerstone/schemas/professional-credential.json",
    "type": "JsonSchema"
  }
}
```

## 8. References

### 8.1 Industry References

- **vLEI Engagement Context Role (ECR)** — Organizational role credential where status changes are handled by revoking/reissuing, not by embedding status. [gleif.org/vlei](https://www.gleif.org/en/organizational-identity/introducing-the-verifiable-lei-vlei)
- **W3C Verifiable Credentials Data Model 2.0** — [w3.org/TR/vc-data-model-2.0](https://www.w3.org/TR/vc-data-model-2.0/)
- **W3C Bitstring Status List v1.1** — [w3c.github.io/vc-bitstring-status-list](https://w3c.github.io/vc-bitstring-status-list/)

---

**Document Control**

- **Owner**: Cornerstone Network
- **Governance Body**: Cornerstone Network
- **Review Cycle**: Annual or upon breaking schema changes
- **Schema Registry**: `https://trustinfrastructure.com/cornerstone/schemas/` *(exact URL TBD)*
