# Risks and technical debt

| Priority | Finding | Impact / likelihood | Next action / approval gate |
|---|---|---|---|
| High | No initial commit yet | No durable Git recovery point; certain until commit | Review exact candidates and obtain separate staging/commit approval |
| High | Secrets/runtime can use arbitrary filenames | Leakage possible despite ignore patterns | Review every commit; never store credentials in state/config/examples |
| High | Hosting/authenticated REST boundaries unresolved | Security/architecture impact if inferred from scaffolds | Approved design review before dependent implementation |
| High | Signing/activation/keys not designed or verified | Invalid trust, replay/time ambiguity or key loss | Resolve contracts, interoperability and recovery before production signing |
| High | Upload scanner, backup consistency and floating consistency TBD | Unsafe files, data loss or oversubscribed seats | Resolve each gate before implementing its production workflow |
| Medium | Stale generated Web assets reference undeclared packages | Current build reproducibility unknown; observed | WU-02 only after approval |
| Medium | Single placeholder unit test | No business coverage; observed | Add real unit coverage with approved capability units |
| Medium | Unit-only automated test scope | Infrastructure correctness remains unproven | Request separate verification approval when needed |
| Medium | Layer-first scaffold lacks module implementation | Boundary drift possible as code grows | Review lean module organization; no restructuring in WU-01 |
| Medium | Exact SDK pin and unspecified deployment matrix | Provisioning/servicing and platform support risks | Verify supported targets and maintenance approach in approved future work |
| Medium | UX/capacity/acceptance details TBD | Cannot prove accessibility/performance readiness | Agree criteria before dependent delivery |

No risk entry grants permission to widen a work unit. See `decisions.md` for
baseline tensions and unresolved choices; retain the original specifications.
