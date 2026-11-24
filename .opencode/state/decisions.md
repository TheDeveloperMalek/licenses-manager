# Operational decisions and unresolved questions

These records supplement the immutable specifications; they do not rewrite the
baseline or approve unresolved product/technical choices.

## Approved

- **WU-01:** Product Owner explicitly approved local Git initialization on `main`,
  narrow ignore safeguards, `opencode.jsonc` explicitly loading all four original
  files, and the five operational records in `.opencode/state/`.
  Rationale: protect existing work and establish safe project continuity.
- Preserve every existing application file and all four specifications unchanged.
- Keep safe templates, AGENTS.md, and durable OpenCode assets eligible for Git.
- Initial safeguards approval did not authorize staging/commit or remote/push.
  The subsequent explicit approval below supersedes those Git restrictions only.
- **Initial baseline publication approved:** stage the reviewed baseline, create
  `chore: establish initial scaffold and project workflow safeguards`, configure
  origin as `https://github.com/TheDeveloperMalek/licenses-manager.git`, and push
  main with upstream tracking if preservation, secrets, license, staged-diff and
  remote-history checks pass. Never force-push or overwrite unrelated history.
- **Future Git workflow approved:** use Conventional Commits; automatically commit
  subsequent approved work units after successful implementation and required
  validation. Commits must contain only the reviewed, relevant, non-sensitive
  paths within the approved unit. Obtain explicit approval before every
  subsequent push; this initial push authorization is not continuing permission.
- Builds/tests are not mandatory for this initial behavior-preserving baseline
  commit; that exception does not waive validation for later work units.
- Global configuration changes, dependency installs, application service startup
  and WU-02 remain unapproved.
- **Third-party notices approved:** expand the reviewed 86-file baseline by
  adding only the upstream Bootstrap and bundled Popper MIT notices. Final
  approved inventory: 88 paths in `work-units.md`. Existing application files,
  specifications and vendored assets must remain byte-identical.
- Verified Bootstrap 5.3.3 against all 44 official release assets. Its release
  lockfile resolves bundled @popperjs/core 2.11.8; source maps show no other
  external bundled dependency. Original notices are copied byte-for-byte from:
  - https://raw.githubusercontent.com/twbs/bootstrap/v5.3.3/LICENSE
  - https://raw.githubusercontent.com/floating-ui/floating-ui/v2.11.8/LICENSE.md
- These notices apply to their third-party software only. No license is granted
  or introduced for the application's own source code.
- **One-time whitespace exception APPROVED by Product Owner:** waive only the pre-existing
  `NavMenu.razor:30` final-blank-line finding for the initial baseline commit.
  `git diff --cached --check` exits 2; changing the application file would
  violate the preservation requirement. Preserve all 409 originals unchanged.
  Do not disable, suppress or weaken future whitespace validation. This exception
  applies only to this one finding in the initial 88-file baseline publication.

## Open: specification tensions

- `plan.md` Phase 0 proposes adding CLAUDE.md. The immutable-file rule prohibits
  editing/replacing the original; that plan item must not modify it.
- Backup acceptance excludes secrets from backups while key recovery must remain
  possible. Exact handling of encrypted signing-key material versus separately
  provisioned Master Key/recovery material needs explicit clarification.
- WCAG 2.2 AA is the target; the practical verification process/checklist is TBD.

## Open: decisions before dependent implementation

- API/Web topology, authenticated REST calls, cookie/CSRF boundaries, API versioning
  and error contract; current separate scaffolds do not settle these choices.
- Module organization and dependency versions; existing scaffolding is evidence,
  not a changed architecture approval.
- Permission catalog, user/role administration, sessions and concurrent sessions.
- JWS profile/claims, Ed25519 library interoperability, first-activation evidence,
  replay/clock handling, signing-key rotation, Master Key provisioning/recovery.
- Fingerprint weights/defaults/precedence and missing identifiers; dongle vendor,
  SDK/protocol and proposed (not confirmed) Retired state.
- Floating-server deployment/protocol, seat consistency and restart recovery.
- Customer/product fields, custom durations/grace defaults, attachment policy and
  local AV scanner/outage/update procedure, notification catalog/preferences.
- Backup consistency/retention/verification, optional encryption, certificates,
  Windows/Linux installation, capacity/performance targets and detailed UX choices.

Candidate libraries, fonts, proposed navigation and illustrative notification lead
times remain candidates/proposals/examples. No architectural decision was made
in WU-01. Existing out-of-scope exclusions remain unchanged.
