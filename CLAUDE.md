# Project Instructions

## Mission
Build an internal, on-premises, offline-first system for managing and protecting licenses for multiple software products. The system is not a SaaS product and has no customer portal. Administrators manage customers, products, licenses, devices, dongles, requests, users, notifications, audit history, backups, and settings.

## Non-negotiable engineering principles
- Keep the code human-readable, scalable, maintainable, and very lean.
- Apply SOLID, DRY, and KISS. Prefer the simplest design that satisfies a real requirement.
- Use design patterns only when they solve a demonstrated problem. Avoid speculative abstractions, needless interfaces, generic frameworks, and over-engineering.
- Optimize for correctness, security, and measured performance in both backend and frontend. Do not add caches or complexity without a clear invalidation and correctness strategy.
- Preserve established behavior unless a requirement explicitly calls for a change. Never silently reinterpret an approved business rule.
- Keep business rules out of UI components, API endpoints, and persistence details.

## Architecture constraints
- Backend: ASP.NET Core / C#, Clean Architecture, Modular Monolith, PostgreSQL, EF Core.
- Organize backend code by business module and layer. Expected modules: Licensing, Customers, Products, Devices, Dongles, Requests, Identity & Access, Notifications, Audit, and System Settings. User Guide content is static documentation embedded in the application, not a CMS/database module.
- Use CQRS with MediatR: commands change state; queries read state. Keep handlers focused. Add pipeline behaviors only for cross-cutting concerns that genuinely benefit from them.
- The application is API-first. Blazor Web App (Interactive Server with Static SSR where appropriate) consumes REST APIs; UI code must not call application services or the database directly.
- Use ASP.NET Core Identity for authentication and custom fine-grained RBAC for authorization. Every user has one role; users have global data visibility, while permissions control actions.
- Use EF Core and a shared PostgreSQL schema. Use Hangfire for background jobs and SignalR for real-time notifications; persist notifications in PostgreSQL.
- API description uses OpenAPI; use Scalar as the API reference UI, not Swagger UI.
- Store uploaded files on the local filesystem and metadata in PostgreSQL. Backups must account for database/files consistency.
- Use Serilog structured logging. Never log passwords, private keys, master keys, tokens, or other secrets.
- Target Windows and Linux native installations. Do not introduce a Docker requirement.

## Security and licensing invariants
- Licensing is fully offline for activation, validation, renewal, and revocation using manually transferred files. The administration dashboard itself requires access to its local server.
- License files use JWS with Ed25519/EdDSA signatures. They are signed, not encrypted. The licensed application contains the public key; the central system protects the private signing key.
- Never claim that signatures alone prevent binary patching or removal of validation checks.
- Protect the encrypted private-key file using a Master Key supplied through the service environment. Never put real secrets in source control, `appsettings`, logs, examples, or generated documentation.
- Use composite hardware fingerprints and weighted matching. The matching threshold is configurable; final component weights and defaults remain TBD. Below-threshold match means immediate block.
- If a required dongle cannot be read, block immediately. Keep dongle integration vendor-neutral until a vendor/SDK is selected.
- Floating licensing uses a local license server on the customer LAN. Approved initial heartbeat is 15 seconds and disconnect timeout is 60 seconds. Release seats on normal shutdown or timeout. Do not represent floating licensing as network-free.
- Offline revocation cannot take effect on a disconnected client until the signed revocation file reaches and is imported by that client.

## No magic strings
- Frontend user-facing text must use centralized `translationKeys`; support Arabic/English and RTL/LTR.
- Backend configuration keys and stable domain constants must be centralized in domain-specific constants classes. Do not pass raw configuration-key strings throughout the codebase.
- Avoid unexplained string literals for statuses, policies, and protocol identifiers. Prefer typed values or centralized constants appropriate to the boundary. Do not create a constants class for every incidental literal.
- `appsettings.example.json` is the only appsettings JSON template allowed in Git. Real `appsettings*.json` files must remain ignored. Use environment variables or secure local configuration for secrets.

## Frontend and UX
- Blazor Web App with responsive Desktop, Tablet, and Mobile layouts.
- Classic Enterprise visual direction; Navy Blue primary theme; Light and Dark modes with manual switching.
- Collapsible, grouped sidebar navigation; hybrid dashboard.
- Compact desktop tables; on mobile use a hybrid compact-list/card presentation with record details available on demand.
- Use modal dialogs for create/edit/operations unless a future approved requirement demonstrates a complex workflow that needs a dedicated page.
- Dedicated Arabic and English font families; outline icons; toast feedback for quick outcomes and inline feedback for validation/errors.
- Implement comprehensive loading, empty, error, access-denied, disconnected, and unavailable states.
- Target WCAG 2.2 AA. Respect keyboard, screen-reader, contrast, focus, and reduced-motion needs.

## Testing and verification
- The approved initial test strategy is Unit Tests only. Do not add Integration or E2E test projects without explicit approval.
- Unit tests must cover domain rules, command/query handlers, validators, and important edge cases. Be explicit that unit tests do not prove database, filesystem, SignalR, Hangfire, or deployment integration.
- After changes, run the narrowest relevant formatter/build/type checks and unit tests available. Do not claim checks passed unless they were actually run.
- Review security-sensitive changes carefully, especially signing keys, file uploads, authorization, offline revocation, backup/restore, and floating-seat allocation.

## Working agreement
- Before implementation, inspect the existing repository and follow its established conventions where they do not conflict with these requirements.
- For ambiguous business behavior, stop and ask one focused question rather than inventing a rule.
- Keep changes small and cohesive. Explain important trade-offs and mark unresolved decisions as TBD.
- Do not silently add out-of-scope features such as customer portal, SaaS tenancy, feature-based licensing, MFA, password recovery, email notifications, cloud backups, general-purpose report/export modules, custom request types, or automatic historical-log deletion.
