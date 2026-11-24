# Project status

## Verified position

- Lifecycle: foundation scaffold; business capabilities are not implemented.
- Original specifications: `CLAUDE.md`, `plan.md`, `architecture.md`, and
  `design.md` remain permanently read-only. These notes do not replace them.
- Existing solution: five `net10.0` source projects and one unit-test project.
- SDK pinned to 10.0.111 with roll-forward disabled; onboarding found it installed.
- API/Web contain scaffolding; Domain/Application/Infrastructure have no business
  code. The only unit test is a runner placeholder, not business coverage.
- Existing build outputs are not evidence of current-source build success.

## Workflow

- WU-01 completed: local Git on `main`, ignore safeguards, explicit specification
  loading, and five concise durable operational state files; verification passed.
- Initial staging/commit, the named GitHub origin, and initial main push were
  subsequently approved subject to successful pre-publication verification.
- The third-party license-text gap was resolved with explicitly approved,
  additive upstream Bootstrap 5.3.3 and Popper 2.11.8 MIT notices.
- WU-02 is not approved.
- No application/specification edits, dependency installs, application service
  startup, global configuration changes, or architecture decisions are permitted
  in WU-01.
- See `work-units.md` and `work-log.md` for completion and verification status.

## Asset handling

- Share: specifications, AGENTS.md, safe project configuration/templates, source,
  tests, vendored browser dependencies, and these durable operational records.
- Keep local: Git metadata, bin/obj, IDE state, runtime logs/databases, and
  temporary verification evidence. Never store credentials in these notes.
- No project-local OpenCode runtime directories existed at initial inspection;
  do not invent runtime directory names or ignore `.opencode/` wholesale.
- Ignore rules are safeguards, not a guarantee that arbitrary secret/session
  filenames are safe. Review each proposed commit.

## Next action

Pre-commit checkpoint: Product Owner approved the one-time exception for the
existing `NavMenu.razor:30` final blank line. Preserve all 409 originals unchanged;
future whitespace validation is not weakened. Approved publication inventory:
88 files, including two required third-party notices.
Origin: `https://github.com/TheDeveloperMalek/licenses-manager.git`.
Initial commit/push are authorized after remaining checks; verify Git history,
remote main and upstream tracking for the actual publication result.
Future approved work units use automatic Conventional Commits after required
validation; every subsequent push requires explicit approval.
Builds/tests were not run. WU-02 remains unapproved/unstarted.
