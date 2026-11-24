# Software Architecture Specification

## 1. System context and scope

The system is a private, on-premises web application used by administrators to manage multiple software products and their licenses. It runs on a local server or within a customer/organization LAN and must support air-gapped installations. It is not SaaS and does not expose a customer portal.

Administrators interact with customers outside the system and record requests and decisions internally. License activation, validation, renewal, and revocation are fully offline file-based workflows for the licensed products. The administration application itself requires access to its local server.

## 2. Approved technology baseline

| Concern | Approved direction |
|---|---|
| Backend | ASP.NET Core / C# |
| Architecture | Clean Architecture + Modular Monolith |
| Application pattern | CQRS with MediatR |
| Web UI | Blazor Web App, Interactive Server with Static SSR where appropriate |
| UI-to-backend | API-first REST; UI consumes APIs and does not call application services/DB directly |
| API description/UI | OpenAPI + Scalar (not Swagger UI) |
| Database | PostgreSQL, shared schema |
| Data access | EF Core |
| Authentication | ASP.NET Core Identity, username/password, cookie-based |
| Authorization | Custom dynamic RBAC, one role per user, fine-grained permissions |
| Background jobs | Hangfire, PostgreSQL storage; logically separate Hangfire tables |
| Real-time | SignalR; persist notifications in PostgreSQL |
| Cache | In-memory cache initially; never authoritative for licenses or floating seats |
| Logging | Serilog structured local logs |
| File storage | Local filesystem + PostgreSQL metadata |
| License signature | JWS using Ed25519 / EdDSA |
| Deployment | Native Windows and Linux services; no Docker requirement |
| Tests | Unit tests only initially; integration/E2E require approval |

## 3. Architectural principles

- Keep the system a Modular Monolith: one main backend deployment with clear business-module boundaries. Do not split modules into microservices without a demonstrated operational need and explicit approval.
- Apply Clean Architecture dependency direction: Domain at the center; Application depends on Domain; Infrastructure implements Application abstractions; API/UI are delivery mechanisms.
- Organize by business capability first, then by layer. Avoid a single global folder structure that scatters one feature across the repository.
- Use CQRS with MediatR. Commands represent state changes; queries represent reads. Keep handlers cohesive and avoid pass-through layers that add no value.
- Add MediatR pipeline behaviors only for genuinely cross-cutting concerns such as validation, transaction boundaries, or consistent request logging where justified.
- Use SOLID, DRY, KISS, and design patterns only where they reduce complexity or isolate a real variation point.
- No speculative generic repository/unit-of-work layer over EF Core. Use focused abstractions only where they protect domain boundaries or enable a required capability.
- Measure performance and optimize query shape, indexes, payloads, rendering, and resource use based on evidence.

## 4. Logical module boundaries

Expected modules:

1. **Licensing:** license templates, issued licenses, signing/issuance, renewal, transfer, reissue, suspend/resume/revoke/archive/restore, policy snapshots, compatibility ranges.
2. **Customers:** individual/company/organization profiles, contacts, archive/restore.
3. **Products:** product catalog and product-version lifecycle.
4. **Devices:** device records, composite hardware fingerprint, license-device binding and history.
5. **Dongles:** inventory, assignment, unbinding, replacement, lost/damaged/recovery lifecycle and history.
6. **Requests:** fixed request types and approval/review lifecycle, attachments, internal notes, activity timeline, reopen history.
7. **Identity & Access:** users, roles, permissions, authentication, account lifecycle.
8. **Notifications:** in-app notifications, per-event preferences, expiration alerts.
9. **Audit:** immutable/append-oriented audit records for relevant system and licensing operations.
10. **System Settings:** global defaults, security, storage, backup/restore, system information.
11. **User Guide:** static embedded documentation shipped with the app; not a database/CMS module.

Modules share one PostgreSQL schema but should own their application/domain logic and avoid direct cross-module table manipulation. Cross-module use should go through explicit application contracts or domain events where needed; do not introduce an event bus framework unless justified.

## 5. Suggested solution shape

The exact project names are implementation choices. A possible structure is:

```text
/src
  /Host                  # ASP.NET Core composition root, middleware, hosting
  /Presentation
    /Web                 # Blazor UI, layouts, components, translation resources
    /Api                 # REST endpoints, auth, OpenAPI, Scalar
  /BuildingBlocks        # Small shared primitives only; avoid dumping business logic here
  /Modules
    /Licensing
      /Domain
      /Application
      /Infrastructure
    /Customers/...
    /Products/...
    /Devices/...
    /Dongles/...
    /Requests/...
    /IdentityAccess/...
    /Notifications/...
    /Audit/...
    /SystemSettings/...
/tests
  /UnitTests
/docs
  /user-guide
```

This is a logical starting point, not a mandate to create empty projects for every folder. Keep the number of projects and abstractions lean. Static User Guide content may live under the web project or a dedicated documentation content directory and be packaged with the application.

## 6. Request and application flow

Typical write flow:

1. Blazor UI submits an HTTP request to a REST endpoint.
2. API endpoint performs transport-level binding and authentication/authorization checks.
3. Endpoint sends a command through MediatR.
4. Application handler validates use-case rules and invokes domain behavior.
5. Infrastructure persists changes through EF Core/PostgreSQL and required local file operations.
6. Audit records and domain/application events are produced consistently with the state change.
7. API returns a stable response contract; SignalR may notify connected clients after persisted state is committed.

Typical read flow:

1. Blazor UI requests data through REST.
2. API authorizes the request and sends a query through MediatR.
3. Query handler uses EF Core projections/read-optimized queries.
4. API returns a paginated DTO; UI renders it without direct database/application-service access.

For Interactive Server rendering, browser-to-server UI events are handled by Blazor; calls to the REST API may originate server-side. Do not bypass the API boundary by injecting application handlers into UI components.

## 7. Authentication and authorization

- ASP.NET Core Identity handles username/password authentication and password hashing.
- Use cookie-based authentication with HTTPS, HttpOnly, Secure, appropriate SameSite policy, and CSRF protection for state-changing requests.
- Session expiration, revocation, and concurrent-session policy remain TBD.
- Custom RBAC supports dynamic roles and fine-grained permissions. Each user has exactly one role.
- All users share global data visibility; authorization controls which operations they may perform.
- Enforce authorization at the API/application boundary. UI navigation hiding is only a usability feature.
- Account operations include create, edit, activate, deactivate, delete, and manual password reset by an authorized administrator.
- Deleting a user must not delete historical records created by that user.
- No MFA/2FA, forgot-password, email recovery, or social login in current scope.
- Exact permission catalog and rules governing who may manage users/roles are TBD.

## 8. Licensing architecture

### 8.1 License model

- Each license belongs to exactly one customer and one product.
- A customer may own multiple licenses for the same product.
- License types: Lifetime, Subscription, and Custom.
- License types are reusable templates. Each issued license stores a snapshot of the template settings at issuance.
- Editing a template affects new licenses only, unless a separately approved migration policy is defined.
- Keep global defaults separate from per-product and per-license settings. Changing a global default must not silently mutate existing templates or issued-license snapshots; any migration behavior requires an explicit policy.
- All license types enable all product features; feature-based licensing is out of scope.
- Device count is configurable. Binding strategies: HardwareBound, DongleBound, HybridBound, and Unbound.
- Start date is the first successful activation on a customer device. Reissuing a file for the same device does not reset first activation or expiration.
- Renewal preserves License ID, existing device bindings, and dongle binding. Early renewal extends from current expiration; late renewal starts from the prior expiration date.
- Product-version compatibility is a minimum/maximum range. Updating the range requires a manually issued updated license file.

### 8.2 License policies and lifecycle

Expiration policies supported:
- Block Access.
- Grace Period then Block.
- Read Only.
- Continue Working but no support/updates.

Grace period is configurable per template or disabled. Subscription presets: one, three, six, twelve, and twenty-four months; custom durations remain TBD.

Lifecycle operations: Activate, Suspend, Resume, Revoke, Renew, Reissue, Archive, Restore. Every operation must be audited. Activation and renewal require manual approval. Transfer requires manual approval and is atomic centrally: invalidate old binding and bind new device in one central transaction, preserving the relationship between old and new device history. No maximum transfer count is defined. Device replacement is a separate approved request, preserves License ID/settings, and has no maximum count.

### 8.3 Offline licensing and cryptography

- Fully offline manual file exchange for activation, renewal, and revocation.
- Device information can be supplied by uploaded file or manual entry.
- License payload is signed, not encrypted; payload contents are readable.
- Use JWS with `alg: EdDSA`, Ed25519 key material, and `kid` for key identification.
- Licensed application embeds the corresponding public key. Central system holds the private signing key.
- Reject unsupported algorithms and never accept `alg: none`.
- A precise JWS profile, canonical payload rules, claim schema, key rollover behavior, and cross-language test vectors are required before client implementations are finalized.
- Do not assume .NET built-in cryptography or a JOSE package supports Ed25519/EdDSA as needed. Verify library support and interoperability; Bouncy Castle is a candidate, not a final selection.
- Digital signatures prove authenticity/integrity, not resistance to binary patching or removal of validation logic.
- Revocation is distributed as a signed revocation file. An offline device cannot learn about revocation until it imports that file.

### 8.4 Private key protection
- Store the private signing key in an encrypted file on the central server.
- Master Key is supplied through the service environment (approved choice); do not place it in appsettings, source control, logs, or ordinary backups.
- Restrict service-account access to the encrypted key file and environment configuration.
- Master Key algorithm/derivation, environment provisioning, rotation, disaster recovery, and secure operator procedures are TBD.
- Loss of required key material can prevent future signing; define and test recovery before production.

## 9. Hardware fingerprint and dongles

### 9.1 Fingerprint
- Composite fingerprint may include Motherboard UUID, CPU identifier, BIOS UUID, disk serial, and system UUID, subject to platform availability and privacy/security review.
- Use weighted matching with tolerance.
- Threshold is configurable. The exact scope of overrides (global, product, template, license) must be resolved; current direction is configurable by product/license policy.
- Final weights and default threshold are TBD and must be validated against real hardware-change scenarios.
- Below threshold means immediate block; no grace period/read-only fallback.

### 9.2 Dongles
- Use a vendor-neutral abstraction with adapters for a selected vendor/SDK later.
- Do not hardcode a vendor before selection.
- If a required dongle cannot be read, block immediately.
- Track serial number, state, customer, license/product association, binding/unbinding dates, and full history.
- States: Available, Assigned, Lost, Damaged; Retired is proposed but requires final confirmation.
- New dongle starts Available. Unbinding returns it to Available and preserves history. Lost/Damaged cannot be reused until reviewed and manually restored. Replacement requires approval and retains old-dongle history.
- Challenge-response protocol, anti-cloning properties, vendor SDK, and operational recovery remain TBD.

## 10. Floating licensing

- Support a dedicated local license server within the customer's LAN to manage concurrent seats.
- Floating runtime requires LAN connectivity to that local server; it is not standalone offline operation.
- Initial approved values: heartbeat every 15 seconds; release seat after 60 seconds without valid heartbeat; release immediately on normal client shutdown.
- Define lease ownership, duplicate-client behavior, clock handling, network partitions, server restart recovery, stale-seat cleanup, and concurrency consistency before implementation.
- In-memory cache must never be the source of truth for seat allocation.
- Whether the floating server is a separate executable/service or a host within the existing deployment is TBD. Keep licensing contracts reusable so the deployment boundary can be decided without rewriting domain logic.

## 11. Customers, products, devices, and dongles

### Customers
- Types: Individual, Company, Organization.
- Common fields plus type-specific fields; multiple contacts with a primary contact.
- Capture business/contact/address/tax details as applicable; exact field catalog TBD.
- Soft delete/archive only; no permanent delete from the UI.
- Archiving a customer does not affect existing licenses. New requests/operations are blocked until restored.

### Products and versions
- Product stores core identity such as name and description.
- Licensing/security configuration is separate from the product identity record.
- Each product has a version catalog.
- Version states: Active (new activations/use), Inactive (no new activations; existing licenses continue), Deprecated (old/not recommended; existing licenses continue).
- Product archive hides it from active lists while existing licenses continue; restore returns it to Active.

### Devices
- Keep current and previous device records and binding history.
- Device replacement uses a separate approved request; preserve License ID and license settings.

## 12. Requests and attachments

Fixed request types only:
1. Activation
2. Renewal
3. Transfer
4. Device Replacement
5. Dongle Replacement

Lifecycle: Draft → Pending → Under Review → Approved/Rejected → Completed.

- Reopen the same request with the same Request ID; preserve rejection, closure, reopening history and reasons.
- Multiple attachments can be added at any stage, including after closure.
- Common office, image, text, and JSON formats are required; exact allowlist, maximum size, and content policy are TBD.
- Store files locally and metadata in PostgreSQL.
- Validate extension, MIME type, and actual file signature. Scan with a local antivirus engine (ClamAV is a candidate; platform packaging and Windows/Linux integration must be verified). Do not make files available for normal use until scanning succeeds. Define fail-closed behavior when scanner is unavailable.
- Prevent path traversal, unsafe filenames, unauthorized downloads, and storage paths derived directly from user input.
- Internal Notes are private administrative notes and are distinct from official Request Activity Timeline/comments/decisions. Notes persist after reopening.

## 13. Notifications, audit, and logging

### Notifications
- In-app only; no email or external notifications.
- Cover requests, activations, renewals, transfers, replacements, license expiration/approaching expiration, and other approved lifecycle events.
- Per-event preferences are independent.
- Expiration lead times are configurable per license template; examples such as 30/7/3 days are illustrative, not fixed defaults.
- Persist notifications in PostgreSQL so they can be retrieved after reconnect. SignalR is a delivery mechanism, not the durable store.
- Final event catalog and who can configure preferences are TBD.

### Audit
- Record licensing operations with operation, timestamp, old/new state, actor, and reason where applicable.
- Maintain device/dongle histories and request activity timelines.
- Maintain global user activity audit with user, operation, module, timestamp, and old/new values where safe.
- Never record passwords, private keys, Master Key values, authentication tokens, or other secrets.
- Retain records indefinitely; no automatic historical log deletion.

### Logging
- Serilog structured local logs with filtering, rotation, and configurable retention.
- Separate operational logs from durable audit records.
- Log correlation IDs and useful diagnostic context without sensitive payloads.

## 14. File storage and backup

- Store uploaded files on local filesystem; store metadata and relationships in PostgreSQL.
- Backup must cover database and file content consistently, with restore to a prior valid point.
- Local scheduled and manual backups; no cloud backup.
- Encryption of backups is optional in the approved scope, but key handling and recovery must be designed before production.
- Define atomicity/consistency strategy, backup verification, restore ordering, retention, disk-space handling, and key-material recovery. These are TBD.
- Do not assume a database backup alone is sufficient.

## 15. Configuration and repository hygiene

- Provide `appsettings.example.json` containing configuration shape and safe placeholders only.
- `.gitignore` must ignore real `appsettings*.json` files while explicitly allowing only `appsettings.example.json`.
- Do not commit actual secrets, connection strings, certificates, tokens, or private key material.
- Prefer environment variables or secure OS/service-level configuration for secrets.
- Define domain-specific backend constants classes for configuration keys/sections and stable domain constants; avoid scattering raw string keys. Use the same constants when reading configuration rather than repeating raw section/key paths. For environment variables, document the platform's hierarchical naming convention (for example, double underscores in .NET configuration).
- Suggested `.gitignore` rule (adjust paths for the actual solution layout):

```gitignore
# Ignore all real appsettings JSON files
**/appsettings*.json

# Allow only the safe, committed template
!**/appsettings.example.json
```

- Verify the ignore behavior with Git before the first commit. If a real settings file was already tracked, remove it from the index; adding an ignore rule does not untrack an existing file.
- Frontend UI strings must use centralized `translationKeys`.
- Example configuration must not imply production-safe secret values.

## 16. Background processing and real-time updates

- Hangfire handles scheduled/background tasks such as expiration notifications and backup scheduling.
- Use PostgreSQL storage and keep Hangfire tables logically separated.
- Jobs must be idempotent where retries are possible and must not duplicate notifications or lifecycle transitions.
- SignalR provides real-time UI updates; durable notification state is persisted before delivery.
- Reconnection, duplicate delivery, and missed-event recovery behavior are TBD.

## 17. Performance and scalability

### Backend
- Use async I/O for database, filesystem, and network operations.
- Project only required columns; avoid loading full aggregates for list pages.
- Use pagination for collection endpoints; define stable sorting and limits.
- Avoid N+1 query patterns; inspect generated SQL and query plans for critical paths.
- Add indexes based on query patterns and measured plans.
- Use transactions for operations requiring atomic state transitions, especially license transfer and seat allocation.
- Keep cache in-memory and non-authoritative; define invalidation explicitly.
- Bound background job concurrency and file-scanning work to protect server resources.
- Profile SignalR connection/resource behavior under expected concurrency.

### Frontend
- Avoid unnecessary component re-rendering and oversized payloads.
- Load data on demand; use pagination and server-side filtering for large collections.
- Use virtualization only where measured record counts justify it.
- Lazy-load rarely used screens/resources where appropriate.
- Keep tables compact without compromising accessibility.
- Measure initial load, navigation, and large-list behavior on supported devices.

### Performance validation
- Performance is a cross-cutting requirement. Exact service-level objectives, dataset sizes, concurrent-user targets, and benchmark environments are TBD.
- Advanced optimization should be driven by profiling. Load/stress testing was identified as a desired optimization activity, but the approved formal test strategy is currently Unit Tests only; obtain approval before introducing a separate automated performance-test suite.

## 18. Deployment and operations

- Support Windows and Linux native installation.
- Linux: systemd service. Windows: Windows Service or suitable ASP.NET Core hosting.
- Shared codebase where practical; isolate OS-specific paths, permissions, service setup, certificates, and environment configuration.
- HTTPS is required even on LAN. Certificate provisioning, trust distribution, renewal, and air-gapped operations are TBD.
- The environment may be fully air-gapped; no required runtime cloud dependencies.
- Provide operational health checks and clear diagnostics without exposing secrets.

## 19. Localization and User Guide

- Arabic and English UI and documentation, including RTL/LTR.
- Centralized frontend `translationKeys` for all user-facing strings.
- Static embedded User Guide pages shipped with application releases. No CMS/database-backed guide.
- Organize guidance by module and end-to-end scenario, with prerequisites, steps, expected results, and warnings.

## 20. API design and documentation

- REST API is the application boundary for the Blazor UI and any future approved clients.
- Use stable request/response DTOs and consistent validation/error contracts.
- Publish OpenAPI descriptions and use Scalar as the interactive API reference UI.
- Do not expose internal domain entities directly as API contracts.
- API versioning strategy and error envelope details are TBD.

## 21. Test strategy

Approved initial strategy: Unit Tests only.

Unit tests should cover:
- Domain invariants and lifecycle transitions.
- Fingerprint weighted-match calculation and threshold behavior.
- License date/renewal/expiration policies.
- Command/query handler behavior and validation.
- Permission policy decisions.
- Transfer atomicity at the application/domain level (database transaction behavior itself is not proven by unit tests).
- Request state transitions and reopen history.
- Floating seat lease/timeout rules.
- File validation policy decisions.

Do not claim unit tests prove EF Core/PostgreSQL behavior, filesystem safety, antivirus integration, SignalR delivery, Hangfire scheduling, OS service setup, cryptographic library interoperability, or restore correctness. Those remain risks until additional verification is approved.

## 22. Open decisions and TBD register

### Security and cryptography
- JWS profile, payload schema, canonicalization, key IDs, key rotation, and client interoperability test vectors.
- Ed25519 .NET and JOSE library choice verified by prototype.
- Master Key generation/derivation, environment provisioning, rotation, recovery, and operator procedures.
- Backup encryption and recovery-key strategy.
- HTTPS certificate issuance/trust/renewal in air-gapped installations.

### Licensing and hardware
- Fingerprint component weights, default threshold, and exact configuration precedence.
- Platform-specific hardware identifiers and behavior when an identifier is unavailable.
- Dongle vendor/SDK, challenge-response protocol, failure modes, and test hardware.
- Floating server deployment boundary, protocol, lease consistency, restart recovery, and concurrency model.
- Subscription custom durations and final grace-period defaults.
- Final Dongle Retired state confirmation.

### Product and request details
- Exact customer field catalog and product/version metadata.
- Final role/permission catalog and user/role administration policy.
- Request attachment allowlist, size limits, scanner choice, scanner outage behavior, and update process for AV signatures.
- Notification event catalog and preference-management permissions.
- User Guide content format and navigation/search behavior.

### Operations and performance
- Backup consistency/atomicity, retention, verification, restore workflow, and disaster-recovery objectives.
- Session expiry/revocation and concurrent-session policy.
- API versioning and error contract.
- Performance targets, expected data volumes, concurrent users, and supported deployment sizing.
- Exact Linux/Windows installation and certificate procedures.

## 23. Out of scope

- Customer Portal.
- SaaS/multi-tenant hosting.
- Feature-based licensing.
- MFA/2FA.
- Forgot-password or email recovery.
- Email/external notifications.
- Cloud backups or cloud hosting.
- General reporting/export module.
- Custom request types.
- Automatic deletion of historical records.
- Mandatory Docker deployment.
- Mandatory SDK shared by all licensed products; products may implement the shared licensing specification in their own language.
