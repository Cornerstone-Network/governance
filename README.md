# Cornerstone Network Governance Repository

This repository serves as a **Verifiable Data Registry** for the Cornerstone Network ecosystem.

> **Note**: This is a data registry for credential artifacts. The Trust Registry (managing entities and authority statements) is a separate platform that references this registry for schemas, contexts, and VCT definitions.

## Repository Structure

```
governance/
├── .github/workflows/
│   └── deploy-docs.yml
│
├── site/                              # MkDocs source (published website)
│   ├── index.md                       # Homepage
│   ├── CNAME                          # Custom domain
│   ├── hooks.py                       # Dynamic content generation
│   │
│   ├── governance/                    # Credential governance frameworks
│   │   ├── index.md
│   │   ├── cornerstone-id.md
│   │   ├── home-credential.md
│   │   ├── professional-credential.md
│   │   ├── portfolio-issuer.md
│   │   └── property-access-authorization.md
│   │
│   ├── policies/                      # Network policies
│   │   └── index.md
│   │
│   └── assets/
│       └── images/
│
├── credentials/                       # All credential artifacts (machine-readable)
│   │
│   ├── schemas/                       # JSON Schemas (validation)
│   │   └── {credential-type}.json
│   │
│   ├── contexts/                      # JSON-LD Contexts (semantics)
│   │   └── {credential-type}.jsonld
│   │
│   └── vct/                           # Verifiable Credential Types (branding)
│       └── {credential-type}.json
│
├── mkdocs.yml
├── README.md
└── LICENSE
```

## Folder Definitions

| Folder | Purpose | Consumed By |
|--------|---------|-------------|
| `credentials/` | All credential artifacts (machine-readable) | Third-party apps, wallets |
| `credentials/schemas/` | JSON Schemas for credential validation | Issuers, validators |
| `credentials/contexts/` | JSON-LD contexts for semantic interoperability | Verifiers, linked data processors |
| `credentials/vct/` | Verifiable Credential Type definitions (branding) | Wallet apps, credential viewers |
| `site/` | Human-readable content (MkDocs source) | Website, third-party apps |
| `site/governance/` | Credential governance frameworks | Humans, governance dashboards |
| `site/policies/` | Network policies | Humans, policy engines |

## Standards

This repository follows these standards:

- **[SD-JWT-VC](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-sd-jwt-vc)**: VCT structure for credential type metadata
- **[JSON Schema](https://json-schema.org/)**: Credential data validation
- **[JSON-LD](https://json-ld.org/)**: Semantic context definitions for W3C VC interoperability
- **[W3C Verifiable Credentials](https://www.w3.org/TR/vc-data-model/)**: Credential data model

## Design Rationale

### Three Separate Artifact Types

Based on the SD-JWT-VC specification and W3C VC standards, these are **distinct artifacts** with different purposes:

| Artifact | Purpose | Created By | Consumed By |
|----------|---------|------------|-------------|
| **JSON Schema** | Data validation (required fields, types, formats) | Ecosystem (Cornerstone) | Issuers, validators |
| **JSON-LD Context** | Semantic meaning for machine interoperability | Ecosystem (Cornerstone) | Verifiers, linked data processors |
| **VCT** | Branding/rendering (colors, logos, labels) | Credential Issuers | Wallet apps, credential viewers |

**Key Insight**: The SD-JWT-VC specification explicitly does NOT include JSON-LD contexts in VCT files. VCT focuses on display/rendering metadata, while JSON-LD contexts provide RDF semantics for the W3C VC Data Model. They serve different purposes and remain separate files.

### Versioning

Each document is versioned individually. There are no version folders (like `v1/`). Version information is included within each document as needed.

### URL Structure

All artifacts are accessible at predictable URLs for third-party app integration:

| Resource Type | URL Pattern | Example |
|---------------|-------------|---------|
| Schema | `https://{domain}/credentials/schemas/{type}.json` | `https://openpropertyassociation.ca/credentials/schemas/cornerstone-id.json` |
| Context | `https://{domain}/credentials/contexts/{type}.jsonld` | `https://openpropertyassociation.ca/credentials/contexts/cornerstone-id.jsonld` |
| VCT | `https://{domain}/credentials/vct/{type}.json` | `https://openpropertyassociation.ca/credentials/vct/cornerstone-id.json` |
| Governance Doc | `https://{domain}/governance/{type}/` | `https://openpropertyassociation.ca/governance/cornerstone-id/` |

## Third-Party App Integration

- **VCT Builder**: Fetches `/credentials/vct/` for available VCT files, creates PRs to add new ones
- **External APIs**: Reference `/credentials/schemas/` for validation, `/credentials/contexts/` for JSON-LD
- **Wallets**: Fetch VCT files to render credential displays

## Credential Artifact Relationship

For each credential type (e.g., `cornerstone-id`):

| Artifact | Purpose | Location | URL |
|----------|---------|----------|-----|
| Governance Doc | Human-readable framework | `site/governance/cornerstone-id.md` | `/governance/cornerstone-id/` |
| JSON Schema | Data validation | `credentials/schemas/cornerstone-id.json` | `/credentials/schemas/cornerstone-id.json` |
| JSON-LD Context | Semantic meaning | `credentials/contexts/cornerstone-id.jsonld` | `/credentials/contexts/cornerstone-id.jsonld` |
| VCT | Wallet branding/rendering | `credentials/vct/cornerstone-id.json` | `/credentials/vct/cornerstone-id.json` |

## Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Folders | `kebab-case` | `credentials/` |
| Markdown files | `kebab-case.md` | `cornerstone-id.md` |
| VCT files | `kebab-case.json` | `cornerstone-id.json` |
| Schema files | `kebab-case.json` | `cornerstone-id.json` |
| Context files | `kebab-case.jsonld` | `cornerstone-id.jsonld` |

## Local Development

```bash
# Install dependencies
pip install mkdocs-material mkdocs-macros-plugin

# Run local server
mkdocs serve

# Build site
mkdocs build
```

## License

[LICENSE](LICENSE)
