# Work units

## WU-01 — Repository and workflow safeguards

- Approval: explicit Product Owner approval, including branch `main`.
- Status: safeguards and additive license notices complete; 88-file publication
  approved, including a one-time exception for the existing NavMenu.razor:30
  EOF-blank-line finding. Verify Git history/remote for publication outcome.
- Scope: initialize local Git; add `.gitignore`, `opencode.jsonc`, and
  `status.md`, `decisions.md`, `work-units.md`, `work-log.md`, `risks.md` here.
- Boundaries: preserve all pre-existing files; no architectural choices, app
  changes, installations, application services, global settings, staging, commit,
  remote or push. Do not begin WU-02.
- Acceptance: original-file SHA-256 equality; correct branch; explicit four-file
  instructions; ignored generated/private/secret paths; visible safe templates,
  AGENTS.md and durable OpenCode assets; secret/artifact review; exact candidate
  handoff with empty index and no remotes.
- Verification: Git status/diffs, check-ignore probes (including hypothetical
  filenames without creating files), configuration parsing, complete pre-existing
  file hash comparison and candidate-content review.
- Rollback: additions are isolated. Preserve existing work; removal of Git
  metadata or other destructive rollback requires explicit approval.
- Results: all 409 original files unchanged; 46 ignore and 25 visibility probes
  passed; 86 reviewed candidates and 330 excluded generated files; empty index,
  no commits/remotes, and valid four-file configuration. See `work-log.md`.
- Publication continuation: Product Owner approved the initial Conventional
  Commit and push to the specified GitHub origin, provided all checks pass.
  Restart recovery found no staged files or commits. Product Owner subsequently
  approved missing upstream license notices; two byte-identical MIT license
  texts were added for Bootstrap 5.3.3 and bundled Popper 2.11.8.

## WU-02 — Reproducible foundation assessment

- Status: proposed, NOT APPROVED; do not start.
- Objective: assess current-source restore/build/unit results and stale outputs,
  then propose compatible dependency and module-layout choices.
- Acceptance proposed: observed checks and limitations documented; user work
  preserved; no dependency installation or architecture choice without approval.
- A complete execution proposal must be approved before starting.

Later units remain proposals from onboarding, not implementation permission:
hosting/contracts, platform/bilingual shell, identity/audit, catalog, licensing
security/core, devices/dongles, requests/uploads, floating, notifications,
recovery/deployment, User Guide and release readiness.

## Approved initial publication inventory

88 exact paths: the original 86 reviewed candidates plus the two approved
third-party notices. No build outputs, private settings or runtime data.

```text
.gitignore
.opencode/state/decisions.md
.opencode/state/risks.md
.opencode/state/status.md
.opencode/state/work-log.md
.opencode/state/work-units.md
AGENTS.md
CLAUDE.md
LicenseManager.slnx
architecture.md
design.md
global.json
opencode.jsonc
plan.md
src/LicenseManager.Api/LicenseManager.Api.csproj
src/LicenseManager.Api/Program.cs
src/LicenseManager.Api/Properties/launchSettings.json
src/LicenseManager.Api/appsettings.example.json
src/LicenseManager.Application/LicenseManager.Application.csproj
src/LicenseManager.Domain/LicenseManager.Domain.csproj
src/LicenseManager.Infrastructure/LicenseManager.Infrastructure.csproj
src/LicenseManager.Web/Components/App.razor
src/LicenseManager.Web/Components/Layout/MainLayout.razor
src/LicenseManager.Web/Components/Layout/MainLayout.razor.css
src/LicenseManager.Web/Components/Layout/NavMenu.razor
src/LicenseManager.Web/Components/Layout/NavMenu.razor.css
src/LicenseManager.Web/Components/Layout/ReconnectModal.razor
src/LicenseManager.Web/Components/Layout/ReconnectModal.razor.css
src/LicenseManager.Web/Components/Layout/ReconnectModal.razor.js
src/LicenseManager.Web/Components/Pages/Counter.razor
src/LicenseManager.Web/Components/Pages/Error.razor
src/LicenseManager.Web/Components/Pages/Home.razor
src/LicenseManager.Web/Components/Pages/NotFound.razor
src/LicenseManager.Web/Components/Routes.razor
src/LicenseManager.Web/Components/_Imports.razor
src/LicenseManager.Web/LicenseManager.Web.csproj
src/LicenseManager.Web/Program.cs
src/LicenseManager.Web/Properties/launchSettings.json
src/LicenseManager.Web/wwwroot/app.css
src/LicenseManager.Web/wwwroot/favicon.png
src/LicenseManager.Web/wwwroot/lib/bootstrap/LICENSE
src/LicenseManager.Web/wwwroot/lib/bootstrap/POPPER-LICENSE.md
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-grid.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-grid.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-grid.min.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-grid.min.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-grid.rtl.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-grid.rtl.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-grid.rtl.min.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-grid.rtl.min.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-reboot.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-reboot.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-reboot.min.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-reboot.min.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-reboot.rtl.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-reboot.rtl.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-reboot.rtl.min.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-reboot.rtl.min.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-utilities.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-utilities.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-utilities.min.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-utilities.min.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-utilities.rtl.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-utilities.rtl.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-utilities.rtl.min.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap-utilities.rtl.min.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap.min.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap.min.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap.rtl.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap.rtl.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap.rtl.min.css
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/css/bootstrap.rtl.min.css.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/js/bootstrap.bundle.js
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/js/bootstrap.bundle.js.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/js/bootstrap.bundle.min.js
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/js/bootstrap.bundle.min.js.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/js/bootstrap.esm.js
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/js/bootstrap.esm.js.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/js/bootstrap.esm.min.js
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/js/bootstrap.esm.min.js.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/js/bootstrap.js
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/js/bootstrap.js.map
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/js/bootstrap.min.js
src/LicenseManager.Web/wwwroot/lib/bootstrap/dist/js/bootstrap.min.js.map
tests/LicenseManager.UnitTests/FoundationTests.cs
tests/LicenseManager.UnitTests/LicenseManager.UnitTests.csproj
```
