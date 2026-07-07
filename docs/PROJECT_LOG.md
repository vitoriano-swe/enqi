# ENQI - Project Log

---

# Sprint 0.1 - Foundation

## Date

2026-07-02

---

## Objective

Prepare the development environment and establish the technical and strategic foundations of the ENQI platform.

---

## Completed

### Development Environment

- Git installed and configured
- GitHub repository created
- Visual Studio Code configured
- .NET 8 SDK installed
- Node.js installed
- Docker Desktop installed
- WSL2 configured
- Ubuntu installed
- Development environment validated

---

### Repository

- Official folder structure created
- .editorconfig added
- .gitignore added
- Initial repository committed
- Repository published to GitHub

---

### Product Decisions

- Product Name: ENQI
- Positioning: Enterprise Knowledge Intelligence Platform
- Official Slogan: From Documents to Decisions.
- Architecture: Multi-tenant SaaS
- Backend: .NET 8
- Frontend: Next.js
- Database: PostgreSQL
- Containers: Docker

---

### Product Principles

- Knowledge is the product. Documents are the source.
- Documents are sources of knowledge, not just files.
- Security and privacy are fundamental by design.
- Artificial Intelligence must always be transparent and explainable.
- Access to knowledge must follow organizational context.
- Automation should simplify work, never increase complexity.
- Every feature must generate measurable value for organizations.

---

### Architecture Decisions

- Documents belong to the organization.
- Departments receive permissions.
- Roles define access levels.
- Individual permissions override inherited permissions when authorized.
- LGPD compliance is a design principle, not a feature.

---

### Documentation Created

- ProductManifesto.md
- DomainDefinition.md

---

## Sprint Status

Completed ✅

---

## Notes

The project officially moved from the Environment Setup phase to the Product Foundation phase.

The ENQI identity, positioning and strategic direction were defined before writing any production code.

This decision establishes a long-term architecture focused on organizational knowledge rather than document storage.---

### New Architecture Decisions

- Organizations are the highest security boundary of the platform.
- Documents belong to the organization, never to individual users.
- ENQI will be organized into configurable Workspaces instead of fixed modules.
- Workspaces group documents, permissions, workflows and AI capabilities according to each organization's business structure.
- Artificial Intelligence must always respect the user's permissions before accessing or generating any information.
- The onboarding experience will be guided through a setup wizard focused on minimizing manual configuration.
- Organization data should be automatically populated whenever trusted public sources are available (e.g., CNPJ lookup in Brazil).
- ENQI introduces the Smart Organization Bootstrap, automatically preparing an initial workspace structure based on the organization's profile while preserving full customization.