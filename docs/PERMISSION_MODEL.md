# ENQI - Permission Model

## Status

Draft v0.1

---

# Purpose

This document defines how authorization, access control and security are enforced throughout the ENQI platform.

Rather than describing authentication mechanisms, this document explains who can access organizational resources, under which conditions and according to which business rules.

Every permission model within ENQI must respect the business principles defined in:

- DomainDefinition.md
- SYSTEM_OVERVIEW.md
- MULTI_TENANCY.md

Its primary objective is to ensure that organizational intelligence is always accessed only by authorized people.

---

# Security Philosophy

Security is not an additional feature of ENQI.

It is part of the domain itself.

Every document, knowledge object, workflow, AI response and business decision belongs to an Organization.

Every action performed inside the platform must respect:

- Organization boundaries
- Workspace boundaries
- User identity
- Assigned Roles
- Explicit Permissions
- Business context
- Organizational governance

Authorization is evaluated before any data becomes visible.

No user should ever receive information outside their authorized context.

This principle also applies to Artificial Intelligence.

---

# Authorization Layers

Authorization inside ENQI is evaluated through multiple security layers.

Each layer progressively reduces the accessible organizational context.

Only after every layer has been successfully validated may a user access documents, knowledge or AI-generated information.

The authorization flow follows the hierarchy below.

```text
Organization
        │
        ▼
Workspace
        │
        ▼
User
        │
        ▼
Role
        │
        ▼
Permissions
        │
        ▼
Document Access
        │
        ▼
Knowledge Access
        │
        ▼
AI Context
        │
        ▼
AI Response
```

Every layer must be successfully validated.

Failure at any layer immediately denies access.

Authorization is cumulative.

Being authorized at one layer never implies authorization for the next.

Each layer exists to protect a different business concern.

---

# Layer 1 — Organization

The Organization represents the highest security boundary within ENQI.

Every resource belongs to exactly one Organization.

Organizations are completely isolated from one another.

No document, knowledge object, AI context or workflow may ever cross organizational boundaries.

Every authorization process starts by validating the Organization.

---

# Layer 2 — Workspace

A Workspace defines an independent business context inside an Organization.

Documents, knowledge, workflows and AI agents belong to Workspaces.

A user must explicitly belong to a Workspace before accessing any resource contained within it.

Membership in one Workspace never grants access to another.

---

# Layer 3 — User

Every action inside ENQI is performed on behalf of an authenticated User.

Users never inherit permissions directly from authentication.

Authentication identifies the user.

Authorization determines what the user may access.

Identity alone never grants permissions.

---

# Layer 4 — Role

Roles define what responsibilities a user has within a Workspace.

Roles simplify permission management by grouping common access policies.

Examples include:

- Administrator
- Manager
- Analyst
- Auditor
- Viewer

Roles describe responsibilities.

They do not directly describe access to individual resources.

---

# Layer 5 — Permissions

Permissions represent the smallest authorization unit inside ENQI.

Permissions determine whether a user may:

- View
- Create
- Edit
- Delete
- Share
- Export
- Approve
- Execute AI operations

Permissions may be assigned through Roles or explicit access grants.

The principle of least privilege should always be applied.

---

# Permission Evaluation Flow

Every request processed by ENQI follows the same authorization pipeline.

No business logic may execute before authorization has been fully validated.

The evaluation order is always:

```text
Request
    │
    ▼
Authentication
    │
    ▼
Organization Validation
    │
    ▼
Workspace Validation
    │
    ▼
User Validation
    │
    ▼
Role Validation
    │
    ▼
Permission Validation
    │
    ▼
Resource Classification
    │
    ▼
Context Generation
    │
    ▼
Business Logic
    │
    ▼
AI Context (when applicable)
    │
    ▼
Response
```

If any validation fails, the request immediately stops.

No additional information should be disclosed after authorization failure.

The authorization engine should always follow the Principle of Least Privilege.

Only the minimum required information should become available during each request.

---

# Permission Types

Permissions define the actions that may be performed over organizational resources.

Every permission represents a single business capability.

Permissions should remain granular enough to support flexible organizational policies.

The following permission categories are defined by ENQI.

---

## Read Permissions

- View documents
- View knowledge
- View workflows
- View AI responses
- View organizational metadata

---

## Write Permissions

- Upload documents
- Create knowledge
- Create Workspaces
- Create workflows
- Register AI agents

---

## Update Permissions

- Edit documents
- Edit knowledge
- Edit workflow definitions
- Update Workspace settings
- Update organizational metadata

---

## Delete Permissions

- Delete documents
- Delete knowledge
- Delete workflows
- Delete AI agents

Deletion permissions should always require explicit authorization.

---

## Sharing Permissions

- Share documents
- Share knowledge
- Invite Workspace members
- Grant temporary access

Sharing permissions never override organizational security boundaries.

---

## Administrative Permissions

- Manage Users
- Manage Roles
- Manage Permissions
- Manage Departments
- Manage Workspaces
- Manage Organization settings

Administrative permissions should be restricted to authorized organizational administrators.

---

## AI Permissions

Artificial Intelligence is also governed by permissions.

Examples include:

- Ask AI questions
- Execute AI Agents
- Generate summaries
- Generate reports
- Analyze documents
- Execute knowledge search

AI capabilities should never bypass the authorization model.

Every AI operation is limited to the user's authorized organizational context.

Permissions are additive by design.

Users receive permissions exclusively through Roles, explicit grants or future policy mechanisms.

No permission may implicitly expand the authorized organizational context.

---

# Inheritance Rules

Permissions inside ENQI follow deterministic inheritance rules.

The authorization model must always produce the same result regardless of evaluation order.

## Permission Sources

A user may receive permissions from multiple sources:

- Assigned Roles
- Explicit Permission Grants
- Future Policy-based Rules
- Temporary Access Grants

Permissions from different sources are additive.

## Role-Based Access as the Default

Roles are the primary mechanism for granting permissions inside ENQI.

Explicit and temporary grants are controlled exceptions used for specific business needs.

Every exceptional grant must be:

- Scoped to an Organization
- Scoped to a Workspace or resource
- Granted by an authorized user
- Justified by a business reason
- Auditable
- Revocable
- Time-limited whenever appropriate

Explicit grants may expand capabilities inside an authorized context.

They may never bypass Organization isolation, Workspace isolation, resource classification or governance policies.

---

## Multiple Roles

Users may belong to multiple Roles simultaneously.

Example:

- Financial Analyst
- Invoice Approver
- AI Researcher

The effective permission set is the union of all granted permissions.

---

## Workspace Isolation

Permissions never cross Workspace boundaries.

Being an administrator of one Workspace never grants permissions in another Workspace.

Each Workspace maintains an independent authorization context.

---

## Organization Isolation

Permissions never cross Organization boundaries.

No user, Role or AI Agent may inherit permissions from another Organization.

Organization boundaries are absolute.

---

## Explicit Grants

Explicit permission grants may expand a user's capabilities.

However, explicit grants may never bypass:

- Organization isolation
- Workspace isolation
- Resource classification
- Security policies

Explicit grants are always evaluated inside the existing organizational context.

---

## Temporary Permissions

Temporary permissions may be granted for limited business needs.

Examples include:

- Audits
- External consultants
- Incident response
- Cross-team collaboration

Temporary permissions should contain:

- Start date
- Expiration date
- Grant reason
- Grant owner

Expired permissions immediately become invalid.

---

## AI Authorization

Artificial Intelligence never owns permissions.

AI always executes using the effective permissions of:

- the requesting user,
- or the authorized system identity.

AI can never inherit permissions independently.

---

## Security Principle

Permission inheritance may expand capabilities.

It may never expand organizational boundaries.

---

# Authorization Examples

The following examples illustrate how authorization is evaluated inside ENQI.

---

## Example 1 — Financial Analyst

Organization
    ACME

Workspace
    Finance

User
    Alice

Role
    Financial Analyst

Permissions

✓ Read invoices

✓ Upload invoices

✓ Generate financial summaries

✓ Ask AI about financial documents

✗ Delete invoices

✗ Manage users

✗ Access Legal Workspace

Result:

Alice may use AI only with Finance Workspace documents.

---

## Example 2 — HR Manager

Organization
    ACME

Workspace
    Human Resources

Role
    HR Manager

Permissions

✓ Read employee records

✓ Update employee records

✓ Manage HR workflows

✓ Generate HR reports

✗ Access Finance Workspace

✗ Access Legal documents

Result:

HR permissions remain restricted to the HR Workspace.

---

## Example 3 — Executive

Organization
    ACME

Workspaces

Finance

Legal

Strategy

Role

Executive

Permissions

✓ Read strategic reports

✓ Read financial dashboards

✓ Read legal summaries

✓ Ask AI across authorized Workspaces

✗ Modify confidential legal documents

Result:

Executives may aggregate knowledge across multiple authorized Workspaces.

---

## Example 4 — Temporary Consultant

Organization
    ACME

Workspace
    Finance

Temporary Grant

Invoice Review

Expiration

2026-09-30

Permissions

✓ Read invoices

✓ Generate summaries

✗ Upload documents

✗ Delete documents

✗ Access other Workspaces

Result:

Temporary permissions automatically expire.

---

## Example 5 — AI Authorization

User

Financial Analyst

Authorized Workspace

Finance

Request

"Summarize invoices from this quarter."

AI receives:

✓ Finance documents

✓ Finance knowledge

✓ Finance context

AI never receives:

✗ HR documents

✗ Legal contracts

✗ Other Organization data

Result:

AI visibility is always identical to user visibility.

---

## Authorization Principle

If a user cannot access a resource manually,

Artificial Intelligence cannot access it either.

No exception exists.