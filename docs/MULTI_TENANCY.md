# ENQI - Multi-Tenancy Architecture

## Status

Draft v0.1

---

# Purpose

This document defines how ENQI isolates organizations, users, Workspaces, documents, knowledge and Artificial Intelligence operations inside a multi-tenant SaaS environment.

Multi-tenancy is a fundamental architectural characteristic of ENQI.

It must guarantee that each customer organization operates inside a secure and logically isolated environment, even when infrastructure resources are shared.

---

# Tenant Definition

In ENQI, an Organization represents the tenant boundary.

A tenant is a customer organization that owns and controls its data inside the platform.

Examples include:

- A company
- A law firm
- A hospital
- A government agency
- An educational institution
- A nonprofit organization

Every tenant has its own:

- Organization profile
- Workspaces
- Departments
- Users
- Roles
- Permissions
- Documents
- Knowledge
- Workflows
- AI Agents
- Audit records
- Configuration

No tenant may access, search, process or infer information belonging to another tenant.

---

# Tenant Boundary

The Organization is the highest security boundary inside ENQI.

```text
ENQI Platform
│
├── Organization A
│   ├── Workspaces
│   ├── Departments
│   ├── Users
│   ├── Documents
│   └── Knowledge
│
├── Organization B
│   ├── Workspaces
│   ├── Departments
│   ├── Users
│   ├── Documents
│   └── Knowledge
│
└── Organization C
    ├── Workspaces
    ├── Departments
    ├── Users
    ├── Documents
    └── Knowledge
```

Organizations are completely isolated from one another.

A request made inside Organization A must never return data belonging to Organization B or Organization C.

---

# Workspace Boundary

A Workspace is an independent business context inside an Organization.

A Workspace is not a tenant.

It always belongs to exactly one Organization.

```text
Organization
│
├── Workspace: Finance
├── Workspace: Legal
├── Workspace: Audit 2026
└── Workspace: Acquisition Project
```

Workspaces may contain members from different Departments.

For example, an acquisition Workspace may include people from:

- Finance
- Legal
- Executive Leadership
- Accounting
- Compliance

Workspace access is controlled independently according to organizational permissions.

---

# Department and Workspace Independence

Departments and Workspaces represent different concepts.

A Department represents the formal organizational structure.

A Workspace represents a business or knowledge context.

```text
Organization
│
├── Departments
│   ├── Finance
│   ├── Legal
│   └── Sales
│
└── Workspaces
    ├── Contracts
    ├── Annual Audit
    └── Strategic Planning
```

A Workspace does not belong to a Department.

Users from multiple Departments may collaborate inside the same Workspace when authorized.

---

# Initial Data Isolation Strategy

The initial ENQI architecture will use logical tenant isolation.

The application and infrastructure may be shared, but every tenant-owned resource must include an Organization identifier.

Conceptually:

```text
OrganizationId
WorkspaceId
ResourceId
```

Examples of tenant-owned resources include:

- Documents
- Workspaces
- Departments
- Roles
- Workflows
- Knowledge records
- AI conversations
- Audit events

Every query and operation must be scoped to the authenticated Organization.

---

# Database Strategy

The initial architecture will use a shared PostgreSQL database with a shared schema.

Tenant-owned records must contain an immutable `OrganizationId`.

Example:

```text
Document
├── Id
├── OrganizationId
├── WorkspaceId
├── Name
└── CreatedAt
```

The application must never accept an Organization identifier from the user without validating it against the authenticated context.

Tenant filtering must be applied automatically whenever tenant-owned data is queried.

Future defense-in-depth mechanisms may include PostgreSQL Row-Level Security.

---

# Future Enterprise Isolation Options

The architecture must allow stronger physical isolation for customers with advanced security, compliance or contractual requirements.

Possible future deployment models include:

## Shared Database

Multiple Organizations share the same database and schema while remaining logically isolated by `OrganizationId`.

Suitable for most SaaS customers.

## Dedicated Database

A specific Organization receives its own database while continuing to use the shared ENQI application.

Suitable for customers with stricter isolation requirements.

## Dedicated Environment

A specific customer receives isolated application, database and storage resources.

Suitable for highly regulated or large enterprise customers.

These models must preserve the same domain behavior and authorization rules.

---

# Object Storage Isolation

Documents and binary files must be stored using tenant-aware paths or containers.

Conceptual example:

```text
organizations/
└── {organization-id}/
    └── workspaces/
        └── {workspace-id}/
            └── documents/
                └── {document-id}/
```

Storage identifiers must never be predictable public paths.

Access to stored files must always pass through authorization validation or controlled temporary URLs.

---

# Authentication Context

Every authenticated request must contain a trusted security context.

The context should identify:

- User
- Organization
- Active Workspace, when applicable
- Roles
- Permissions
- Session information

Conceptual example:

```text
AuthenticatedContext
├── UserId
├── OrganizationId
├── WorkspaceId
├── Roles
└── Permissions
```

The Organization context must be established from trusted authentication data, not from unvalidated request input.

---

# Authorization Rules

Authentication answers:

> Who is the user?

Authorization answers:

> What is the user allowed to do inside this Organization and Workspace?

Authorization must evaluate:

- Organization boundary
- Workspace membership
- Department context
- Assigned Roles
- Permissions
- Resource classification
- Explicit access grants
- Governance policies

A valid login does not automatically grant access to every resource in the Organization.

---

# Artificial Intelligence Isolation

Artificial Intelligence must operate under the same security context as the requesting user.

The AI layer must never search, retrieve or process data that the user is not authorized to access.

The secure AI flow is:

```text
User Question
      │
      ▼
Authentication Context
      │
      ▼
Permission Evaluation
      │
      ▼
Authorized Knowledge Retrieval
      │
      ▼
AI Processing
      │
      ▼
Explainable Response
```

Permission filtering must occur before information is provided to the AI model.

AI responses must not reveal:

- Documents from another Organization
- Unauthorized Workspaces
- Restricted metadata
- Confidential information outside the user's permission scope

---

# Search Isolation

All search operations must be scoped by Organization.

Workspace and permission filters must also be applied before returning results.

This applies to:

- Full-text search
- Metadata search
- Semantic search
- Vector search
- AI retrieval
- Knowledge graph queries
- Recommendations

A search index must never allow cross-tenant retrieval.

---

# Background Processing Isolation

Asynchronous operations must preserve tenant context.

Examples include:

- OCR processing
- Document classification
- Metadata extraction
- AI analysis
- Notifications
- Workflow execution
- Search indexing

Every background message or job must contain the minimum trusted identifiers required to restore its Organization and Workspace context.

A worker must validate tenant ownership before processing any resource.

---

# Cache Isolation

Cached data must be separated by Organization.

Conceptual cache keys should include the tenant identifier.

Example:

```text
organization:{organization-id}:workspace:{workspace-id}:resource:{resource-id}
```

Tenant identifiers must never be omitted from cached tenant-owned resources.

---

# Auditability

Every sensitive operation must generate an audit event.

Audit information should include:

- Organization
- Workspace
- User
- Action
- Resource
- Timestamp
- Result
- Relevant security context

Examples include:

- Document access
- Document upload
- Document deletion
- Permission changes
- AI queries
- Workflow approvals
- User invitations
- Data exports

Audit records must remain isolated by Organization.

---

# Data Export and Deletion

Each Organization must be able to manage its own data lifecycle according to contractual and regulatory requirements.

Future capabilities must support:

- Data export
- Account closure
- Retention policies
- Legal holds
- Controlled deletion
- Anonymization when applicable
- Audit evidence

Deletion must consider both active data and derived data such as search indexes, AI embeddings, caches and temporary processing files.

---

# Security Principles

The ENQI multi-tenant architecture follows these principles:

## Deny by Default

Access is denied unless explicitly authorized.

## Least Privilege

Users and services receive only the permissions required to perform their responsibilities.

## Tenant Context Is Mandatory

No tenant-owned operation may execute without a valid Organization context.

## Defense in Depth

Isolation must not depend on a single application check.

Multiple controls should protect data across the application, database, search, storage, cache and AI layers.

## No Cross-Tenant Trust

Resources from different Organizations must never be combined unless a future, explicit and audited business feature defines a secure sharing model.

## Audit Sensitive Operations

Security-relevant actions must be traceable.

---

# Multi-Tenancy Invariants

The following rules must never be violated:

1. Every tenant-owned resource belongs to exactly one Organization.
2. A Workspace belongs to exactly one Organization.
3. A Department belongs to exactly one Organization.
4. A User may only access Organizations in which the user has an active membership.
5. No query may return tenant-owned data without an Organization scope.
6. AI operations may only use information authorized for the requesting user.
7. Search, storage, cache and background processing must preserve tenant isolation.
8. Cross-tenant access is denied by default.
9. Tenant identifiers must be validated from trusted authentication context.
10. Every sensitive cross-boundary attempt must be rejected and auditable.

---

# Initial Architectural Decision

The first ENQI production version will adopt:

- Shared application
- Shared PostgreSQL database
- Shared database schema
- Mandatory `OrganizationId` on tenant-owned records
- Workspace-level authorization inside each Organization
- Tenant-aware object storage
- Tenant-aware search and AI retrieval
- Centralized audit trail
- Architecture prepared for future dedicated customer deployments

This approach balances:

- Security
- Development speed
- Operational simplicity
- SaaS scalability
- Commercial flexibility

---

# Relationship with Other Documents

This document must remain aligned with:

- DomainDefinition.md
- SYSTEM_OVERVIEW.md
- PERMISSION_MODEL.md
- WORKSPACE_MODEL.md
- DOCUMENT_MODEL.md
- KNOWLEDGE_MODEL.md
- AI_ARCHITECTURE.md

---

# Final Principle

Multi-tenancy is not only a database strategy.

It is a security boundary that must be preserved throughout every layer of ENQI.

```text
Organization
      │
      ▼
Authorized Context
      │
      ▼
Authorized Data
      │
      ▼
Authorized Intelligence
```

No organizational intelligence may exist outside its authorized tenant context.