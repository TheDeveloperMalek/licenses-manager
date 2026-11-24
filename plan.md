# Implementation Plan

## 1. Planning rules

- Deliver the system incrementally by vertical business capability.
- Resolve security-critical TBDs before depending on them in production workflows.
- Preserve approved business rules and keep unresolved items explicitly marked TBD.
- Keep the solution lean: do not create a project, abstraction, service, pattern, or background job without a concrete requirement.
- Every milestone must include acceptance criteria and a review of security, performance, localization, and accessibility impact.
- Formal automated test scope is Unit Tests only unless the owner explicitly approves integration, E2E, or performance test suites.

## 2. Phase 0 — Repository and delivery foundation

### Work
- Inspect the repository and confirm existing tooling/conventions before changing structure.
- Establish the solution/project layout aligned with Clean Architecture and modular boundaries.
- Configure ASP.NET Core host, Blazor Web App, REST API, PostgreSQL, EF Core, MediatR, Identity, Serilog, OpenAPI, and Scalar.
- Configure Arabic/English localization infrastructure, centralized frontend `translationKeys`, RTL/LTR support, Light/Dark theme foundations, and responsive application shell.
- Add `appsettings.example.json` with safe placeholders.
- Configure `.gitignore` to ignore real appsettings files and allow only the example template; verify no real secrets are tracked.
- Establish code formatting, analyzers, build commands, and unit test project.
- Add a concise root `CLAUDE.md`; add scoped `.claude/rules/`, Skills, Hooks, or subagents only if a concrete workflow justifies them.

### Acceptance criteria
- Clean build on supported development environments.
- App starts with local non-secret configuration and documented environment variables.
- No real appsettings or secret files are tracked.
- Scalar serves the OpenAPI document.
- UI shell works in both themes and both locales, including RTL.
- Backend and frontend performance baselines are recorded, even if initial targets remain TBD.

## 3. Phase 1 — Identity, access, audit foundation

### Work
- Configure ASP.NET Core Identity with username/password and secure cookie authentication.
- Implement custom roles and fine-grained permissions with one role per user.
- Implement authorization policies at API/application boundaries.
- Implement user account lifecycle and manual password reset by authorized users.
- Establish audit infrastructure and safe logging conventions.
- Resolve the permission catalog and role/user administration policy before completing the administration UI.

### Acceptance criteria
- Unauthorized API operations are rejected regardless of UI visibility.
- Passwords and secrets never appear in logs or audit records.
- User deletion preserves historical attribution.
- Unit tests cover permission decisions and account lifecycle rules.

## 4. Phase 2 — Core catalog: customers, products, versions

### Work
- Implement customer types, shared/type-specific fields, multiple contacts, primary contact, archive/restore.
- Implement products and product version catalog with Active, Inactive, Deprecated states.
- Implement product archive/restore behavior.
- Build responsive list/detail/create/edit experiences using compact tables and modal dialogs.

### Acceptance criteria
- Archived customers cannot initiate new requests/operations; existing licenses remain unaffected.
- Product version state behavior matches approved rules.
- Product archive does not interrupt existing licenses.
- UI text uses translation keys and all screens support Arabic/English and both themes.

## 5. Phase 3 — Licensing domain and cryptographic foundation

### Gate: resolve before production signing
- Finalize JWS profile and claim schema.
- Verify Ed25519/EdDSA support in selected .NET and client libraries.
- Define key ID and rotation behavior, Master Key protection, environment provisioning, and recovery procedures.
- Define cross-language test vectors and validation behavior.

### Work
- Implement reusable license templates and issuance snapshots.
- Implement Lifetime, Subscription, and Custom license rules.
- Implement version compatibility, device limits, expiration policies, grace periods, and first-activation start semantics.
- Implement license lifecycle commands and audit history.
- Implement signed license issuance and verification contract.
- Implement file-based activation, renewal, and revocation exchange.

### Acceptance criteria
- A license file is verifiable offline using the embedded public key.
- Unsupported algorithms and unsigned tokens are rejected.
- Reissue does not reset first activation/expiration.
- Early and late renewal follow the approved date rules.
- Template edits do not mutate existing license snapshots.
- Cryptographic interoperability test vectors pass across supported client implementations; these tests may require a separately approved non-unit verification approach.

## 6. Phase 4 — Devices, hardware matching, dongles

### Work
- Implement device records, composite fingerprint capture contract, binding history, transfer/replacement history.
- Implement weighted matching engine with configurable threshold.
- Determine component weights and defaults through real hardware testing before release.
- Implement vendor-neutral dongle abstraction and inventory lifecycle.
- Select dongle vendor/SDK and challenge-response design before production dongle integration.

### Acceptance criteria
- Below-threshold fingerprint match blocks immediately.
- Missing required dongle blocks immediately.
- Dongle assignment, unbinding, loss, damage, recovery, and replacement preserve full history.
- Device replacement preserves License ID and approved license settings.

## 7. Phase 5 — Requests and secure attachments

### Work
- Implement the five fixed request types: Activation, Renewal, Transfer, Device Replacement, Dongle Replacement.
- Implement Draft → Pending → Under Review → Approved/Rejected → Completed lifecycle.
- Implement reopening with same Request ID and full reason/history preservation.
- Implement Internal Notes separately from official activity timeline.
- Implement local file storage and PostgreSQL metadata.
- Integrate file validation and local antivirus scanning; verify a supported scanner/runtime for Windows and Linux.
- Define scanner-unavailable behavior and safe manual AV signature update procedure.

### Acceptance criteria
- Attachments can be added at any request stage, including after closure.
- Unscanned files are not available for normal download/use.
- Invalid, oversized, malformed, or infected files are rejected according to approved policy.
- Path traversal and unauthorized file access are prevented.
- Internal notes remain private and persist after reopening.

## 8. Phase 6 — Floating licensing server

### Gate: resolve deployment and consistency model
- Decide whether the floating license server is a separate executable/service or a host within the existing deployment.
- Define the wire protocol, seat ownership/lease model, concurrency control, restart recovery, and clock handling.

### Work
- Implement local LAN seat acquisition/release and heartbeat.
- Use approved heartbeat interval of 15 seconds and timeout of 60 seconds.
- Release on normal shutdown and after timeout when heartbeats stop.
- Ensure seat allocation correctness under concurrent requests; do not use in-memory cache as authoritative state.

### Acceptance criteria
- Concurrent acquisitions cannot exceed configured seat count.
- Normal shutdown releases the seat.
- Lost heartbeat releases after the approved timeout.
- Server restart and network partition behavior is deterministic and documented.

## 9. Phase 7 — Notifications, dashboard, and operational visibility

### Work
- Implement persisted in-app notifications and per-event preferences.
- Implement SignalR delivery and reconnect/retrieval behavior.
- Implement Hangfire scheduled jobs for expiration notifications and backups where configured.
- Build hybrid dashboard KPI cards, operational queues, recent activity, notifications, and a small number of useful charts.
- Define final notification event catalog and preference permissions.

### Acceptance criteria
- Notifications remain retrievable after reconnect.
- Retried jobs do not create duplicate notifications.
- Dashboard numbers match underlying domain queries.
- Dashboard does not become a general reporting/export module.

## 10. Phase 8 — System settings, backup, restore, deployment hardening

### Gate: resolve disaster recovery
- Define database/filesystem consistency and backup snapshot strategy.
- Define encryption and key-recovery procedure.
- Define backup verification and restore validation.
- Define HTTPS certificate provisioning and trust distribution for air-gapped environments.

### Work
- Implement general/system settings, licensing defaults, security settings, storage, backup and restore.
- Implement scheduled and manual local backups.
- Implement restore from a selected valid backup.
- Prepare Windows and Linux native installation/service procedures.
- Add health checks, operational diagnostics, log rotation, and resource controls.

### Acceptance criteria
- A restore recovers database and related files to a consistent state.
- Key recovery requirements are met without storing secrets in backups or source control.
- Installation and startup work in supported Windows/Linux environments without cloud dependencies.
- HTTPS works in LAN/air-gapped deployment with documented certificate trust setup.

## 11. Phase 9 — Embedded User Guide and UX completion

### Work
- Author static embedded documentation pages for each module and end-to-end workflow.
- Include prerequisites, step-by-step instructions, expected outcomes, and security warnings.
- Link guide pages from relevant application screens.
- Complete responsive behavior, comprehensive system states, accessibility, and both themes/locales.

### Acceptance criteria
- Guide works without internet access and ships with the application.
- Guide content is available in Arabic and English with RTL/LTR.
- Critical workflows have complete step-by-step guides.
- Key screens meet the agreed WCAG 2.2 AA target as verified by an agreed review process.

## 12. Phase 10 — Release readiness

### Work
- Review all business rules against implemented behavior.
- Review security boundaries: auth, permissions, signing keys, file uploads, AV scanning, backups, offline revocation, and floating seats.
- Review query plans, indexes, pagination, payload sizes, rendering, SignalR, and background job resource use.
- Review localization, responsive layouts, keyboard/screen-reader behavior, and error/reconnect states.
- Produce installation, backup/restore, key recovery, operator, and User Guide documentation.
- Record unresolved risks and obtain explicit acceptance for any deferred controls.

### Acceptance criteria
- No unreviewed critical security or data-loss issue remains.
- All required modules and fixed request types are implemented.
- All approved Unit Tests pass.
- Deployment and recovery procedures are repeatable and documented.
- Remaining TBDs have owners, risk statements, and a release decision.

## 13. Cross-cutting Definition of Done

A feature is not done until:
- Its business rules and authorization are enforced server-side.
- Commands/queries follow the approved CQRS boundaries without needless indirection.
- User-facing text uses `translationKeys` and supports Arabic/English and RTL/LTR.
- Loading, empty, validation, error, permission, and connection states are handled where relevant.
- Audit and logs capture necessary diagnostic/history data without secrets.
- Unit tests cover important domain and application behavior.
- Performance implications are considered and measured when material.
- Accessibility and responsive behavior are reviewed.
- Documentation/User Guide is updated when the workflow changes.
- No real configuration or secrets are committed.

## 14. Dependency and risk register

| Risk / dependency | Why it matters | Required action |
|---|---|---|
| Ed25519/JWS library compatibility | Offline client interoperability and trust depend on exact algorithm support | Prototype .NET and target client libraries; define test vectors |
| Master Key loss or exposure | Can compromise signing or prevent future issuance | Define secure provisioning, rotation, recovery, and operator procedures |
| Hardware identifiers differ by OS | Fingerprint stability and fairness vary by hardware/platform | Test representative Windows/Linux devices and missing identifiers |
| Dongle SDK/vendor unknown | Production integration cannot be completed without hardware/API choice | Keep abstraction vendor-neutral; select vendor before integration |
| Floating server boundary unknown | Affects deployment, consistency, and client protocol | Decide process/deployment model before implementation |
| Antivirus availability on air-gapped Windows/Linux | Upload safety depends on reliable local scanning | Select scanner, package strategy, update process, and fail-closed behavior |
| Backup consistency and key recovery | Incomplete restore can invalidate system or signing ability | Design and rehearse full restore procedure |
| HTTPS in air-gapped LAN | Browser security and cookie protection require trusted certificates | Define CA/certificate provisioning and renewal operations |
| Unit-only test scope | Infrastructure integration risks remain unproven | Track explicitly; seek approval for additional verification if required |
| Performance targets absent | Cannot objectively define scale/readiness | Agree expected users, data volumes, and latency/resource targets |

## 15. Suggested delivery order summary

1. Foundation and configuration hygiene.
2. Identity, authorization, audit.
3. Customers, products, versions.
4. Cryptographic and licensing core.
5. Devices, fingerprint, dongles.
6. Requests and secure attachments.
7. Floating licensing server.
8. Notifications and dashboard.
9. Settings, backup/restore, deployment hardening.
10. User Guide, accessibility, UX completion.
11. Release readiness.

This order may be adjusted after repository inspection and risk prototyping, but security-critical decisions must be resolved before production release.
