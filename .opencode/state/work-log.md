# Work log

## Onboarding — analysis only

- Read AGENTS.md and all four original specifications; inspected source,
  configuration, dependencies, hidden paths and generated outputs.
- Initial `git status --short --branch` and `git diff --stat` failed because
  the folder was not a Git repository. No historical change baseline exists.
- `dotnet --info` confirmed SDK 10.0.111; build/restore/tests were not run.
- No project-local OpenCode configuration/state or ignore rules existed.

## WU-01 — approved execution

- Captured SHA-256 hashes for all 409 pre-existing files before editing.
  Baseline evidence is temporary and outside the repository.
- `git init --initial-branch=main` succeeded.
- Added only seven durable workflow files: `.gitignore`, `opencode.jsonc`, and
  the five state records. No pre-existing file was intentionally changed.
- Configuration uses the documented OpenCode V2 `instructions` field with local
  paths; no providers, credentials, plugins or remote instruction URLs added.
- Verification passed using
  `python3 /tmp/opencode/license-manager-wu01-verify.py` (temporary local script,
  not a durable project tool). It executes these Git checks and Python assertions:
  - `git symbolic-ref --short HEAD`: main.
  - `git ls-files --stage`: empty index.
  - `git remote`: no remotes.
  - `git diff --stat` and `git diff --cached --stat`: empty; all candidates are
    untracked, so these diffs alone do not represent a content review.
  - `git check-ignore --no-index -q -- <path>`: all 46 private/generated probes
    ignored and all 25 durable/template probes visible. No probe files created.
  - `git ls-files --others --exclude-standard -z`: exactly 86 candidates.
  - SHA-256 comparison: all 409 original files unchanged; exactly seven new
    workflow files outside Git metadata. All 330 existing bin/obj files ignored.
  - Python JSON parsing: configuration matches the documented four local
    instruction entries; all target files exist.
  - Targeted credential-pattern checks across 85 UTF-8 candidate files found no
    high-confidence secrets; example PostgreSQL values are placeholders and both
    launch profiles contain only the Development environment variable.
  - Source-map source paths are relative; Bootstrap assets are vendored runtime
    dependencies, not local build outputs. Sole binary candidate is favicon.png,
    visually inspected. This is not an exhaustive dedicated secret/security scan.
- `git status --short --branch --untracked-files=all --ignored` confirmed unborn
  main, untracked candidates, and excluded outputs.
- `git config --local --list` showed only standard repository metadata settings.
  No global Git/OpenCode config was changed.
- OpenCode injected all four configured original instruction files after the
  project configuration was created, confirming instruction discovery here.
- Build, restore, unit tests, services, dependency installs, staging, commit,
  remotes and push: not performed. WU-02 not started.
- Git handoff: 86 exact candidate paths are presented in the final WU-01 report.
  All five state files are durable/shared candidates. Git metadata and existing
  bin/obj remain local; baseline hashes and the verifier stay in `/tmp/opencode/`
  outside the repository. No sensitive files were identified among candidates.

## WU-01 — approved publication continuation and restart recovery

- Product Owner approved the exact initial commit message and initial main push
  to the named GitHub repository, conditioned on all pre-publication checks.
- After server restart, `git status --short --branch --untracked-files=all`
  confirmed no commit/staging had occurred; completed safeguards were not rerun
  as implementation.
- `python3 /tmp/opencode/license-manager-wu01-verify.py` passed again before any
  continuation documentation updates: 409 original hashes unchanged, 86 candidate
  paths, 330 ignored outputs, all ignore/visibility checks and targeted secret
  review passed. A temporary 86-candidate hash snapshot was saved outside Git.
- `git remote -v`: no configured remotes.
- `git ls-remote --symref https://github.com/TheDeveloperMalek/licenses-manager.git`
  succeeded with no advertised refs; no existing remote history was found.
- Bootstrap inspection confirmed existing copyright/MIT-link headers in all
  22 CSS/JS distributions. No full MIT permission text or standalone license file
  exists in the vendored tree. Upstream Bootstrap v5.3.3 LICENSE explicitly
  requires inclusion of both copyright and permission notices.
- Publication paused before staging to request approval for additive upstream
  license text and review any bundled dependency notices. Existing assets and
  specifications have not been modified. No initial commit or push occurred.
- Recorded the approved future Git workflow in `decisions.md`. Updated four
  existing operational records only; this is explicitly requested documentation,
  not an application/specification change.
- Builds/tests remain not run: explicitly waived for the initial
  behavior-preserving baseline, not evidence of application correctness.
- Next: obtain license-file scope approval, verify licenses, review complete
  staged contents, run `git diff --cached --check`, commit and push only if all
  gates pass. Do not begin WU-02.

## WU-01 — license approval and publication preparation

- Product Owner explicitly approved additive original third-party license texts,
  operational record/inventory updates, and proceeding with the initial commit
  and push after successful checks. No additional application license authorized.
- `python3 /tmp/opencode/license-manager-upstream-check.py` verified all 44
  existing vendored files byte-for-byte against official Bootstrap v5.3.3.
  Official release lockfile resolves @popperjs/core 2.11.8; source-map inspection
  found Popper as the only external bundled dependency.
- Added only `src/LicenseManager.Web/wwwroot/lib/bootstrap/LICENSE` and
  `src/LicenseManager.Web/wwwroot/lib/bootstrap/POPPER-LICENSE.md`, copied exactly
  from the corresponding official upstream release tags. Local byte comparisons
  against downloaded upstream originals passed; assets were not rewritten.
- Exact expanded 88-file inventory recorded in `work-units.md`.
- `git ls-remote --symref https://github.com/TheDeveloperMalek/licenses-manager.git`
  again returned successfully with no advertised refs.
- `git remote add origin https://github.com/TheDeveloperMalek/licenses-manager.git`
  configured only the approved local origin; no global configuration changed.
- This is a pre-publication checkpoint. The final staged verification, commit
  and push outcome must be established from actual command results/Git history,
  not assumed from the authorization recorded here.
- Builds/tests remain explicitly waived/not run for this initial baseline.

## WU-01 — final staged checks and preservation blocker

- `python3 /tmp/opencode/license-manager-publication-check.py unstaged` passed:
  all 409 originals unchanged; unchanged non-operational reviewed candidates;
  exact expanded 88-file inventory; all 46 private/generated and 27 durable
  visibility probes passed; all 330 generated files excluded; no high-confidence
  secrets; original upstream license text and 44 official assets verified.
- `git add --pathspec-from-file=/tmp/opencode/license-manager-approved-paths.nul
  --pathspec-file-nul` staged only the exact approved manifest.
- `python3 /tmp/opencode/license-manager-publication-check.py staged` passed:
  88 additions, all staged blobs equal reviewed working files, complete binary
  staged diff inspected by verifier and retained temporarily outside Git.
- `git diff --cached --stat`: 88 files.
- `git diff --cached --check`: FAILED, exit 2:
  `src/LicenseManager.Web/Components/Layout/NavMenu.razor:30: new blank line at EOF.`
  This file is byte-identical to the original 409-file preservation baseline.
- Publication stopped: no original file edited, no check suppressed, no commit,
  no push, and no WU-02. Request a narrowly scoped baseline warning waiver rather
  than silently violate application preservation.

## WU-01 — approved one-time whitespace exception

- Product Owner explicitly approved only the existing trailing blank line at
  `NavMenu.razor:30` for the initial 88-file commit/push. All 409 original files
  must remain unchanged. Future whitespace validation must not be weakened.
- Re-ran `python3 /tmp/opencode/license-manager-publication-check.py staged`
  before updating these operational records: preservation, exact staged
  inventory, ignore rules, targeted secrets review and upstream notices passed.
- Recorded the approved exception in `decisions.md`, updated status/work-unit
  checkpoint, and retained the exact 88-path publication inventory.
- Remaining publication commands: repeat staged review and normal
  `git diff --cached --check`, accept only the precisely approved finding,
  recheck remote history, commit with the approved message, push with upstream,
  and verify HEAD/remote equality and clean index/working tree.
- This record is the pre-commit checkpoint; actual commit hash/push outcomes
  belong to Git history and the completion report, not a prediction in this file.
- Builds/tests were not run, as explicitly permitted for this initial baseline.
  WU-02 remains unapproved and unstarted.
