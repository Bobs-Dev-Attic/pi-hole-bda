# Pi-hole Core Project Review (2026-05-03)

## Scope and method
This review covers repository-level scripts and docs with emphasis on installer flow, CLI controls, security hardening, memory/runtime efficiency, UX, and maintainability. It uses existing documentation as guidance rather than strict constraints.

Reviewed artifacts include:
- `README.md`
- `pihole`
- `automated install/basic-install.sh`
- `advanced/Scripts/utils.sh`
- Test layout in `test/`

---

## Executive summary
Pi-hole core scripts are generally careful and mature (defensive comments, partial exploit mitigations, strict quoting in many paths). The strongest opportunities are:
1. **Security hardening in shell internals** (reduce `eval`, tighten file permissions, safer mutation helpers).
2. **Operational observability and UX** (clearer fail states, non-interactive diagnostics, guided recovery output).
3. **Performance at scale** (batch external process calls in hot paths, cache repeated checks, larger-list optimizations).
4. **Contributor efficiency** (standardized review checklists, priority backlog, and token-efficient docs for AI/code agents).

---

## Findings by domain

## 1) Security and protocol posture

### Strengths
- `pihole` avoids directly sourcing `/etc/pihole/versions`; it uses `loadVersionFile` with key allowlisting and input character restrictions.
- PID handling in `getFTLPID` validates numeric data before usage.
- Most command invocations are quoted, reducing simple argument-injection risks.

### Risks / improvement opportunities
- `loadVersionFile()` still uses `eval` for assignment (`advanced/Scripts/utils.sh`). Even with allowlist + value filter, removing `eval` lowers long-term risk and reviewer burden.
- `addOrEditKeyValPair()` relies on key interpolation inside `grep`/`sed` regex contexts. If future callers pass unsafe keys, this can create malformed replacement behavior.
- Install/update trust model still depends on network git fetches and optional `curl | bash` usage in docs. This is expected for convenience but can be better counterbalanced with stronger provenance controls.

### Recommended upgrades
- Replace `eval` with shell-safe assignment patterns (`case` dispatch by key + direct assignment), or a tiny POSIX-safe mapper function.
- Constrain `addOrEditKeyValPair()` key format using explicit regex check before `grep`/`sed` operations.
- Add optional signature or checksum verification mode for fetched installer/update artifacts and git refs.
- Add hardening checks in install scripts:
  - enforce secure file perms for generated config and secrets,
  - verify umask,
  - report weak permissions in debug output.

---

## 2) Memory usage and runtime efficiency

### Observations
- Scripts are shell-based and generally lightweight, but repeated process spawning (multiple `grep`, `sed`, subshells) can add overhead on constrained systems.
- Large list operations (gravity domains, frequent CLI status requests) can incur repeated IO and process costs.

### Suggested optimizations
- Consolidate repeated file scans into single-pass `awk` where feasible for critical paths.
- Cache expensive environment/probe calls during a single command execution.
- Prefer builtin shell operations for small parsing tasks where maintainability remains acceptable.
- Establish a small benchmark harness for common operations:
  - `pihole -g` with large blocklists,
  - repeated API query operations,
  - install/repair runtime on low-memory profiles.

---

## 3) Best practices and maintainability

### Improvements
- Add a security-focused shell lint profile in CI (e.g., ShellCheck ruleset + banned pattern checks such as unguarded `eval`, unsafe temp files).
- Add architecture and script interaction map for faster onboarding (what invokes what, side effects, privilege expectations).
- Document compatibility matrix for supported distros and minimum tested versions in one canonical file.
- Add explicit non-interactive mode behavior docs and error contract for automation environments.

---

## 4) UX and product design perspective

### User-facing friction points
- Installer and CLI errors can be terse for less experienced users.
- Recovery guidance may require forum/doc lookup rather than inline remediation steps.
- Security tradeoffs (e.g., DNS provider choice, privacy level meaning) could be surfaced more proactively.

### UX improvements
- Add "why this matters" explanations for major installation decisions.
- Provide deterministic troubleshooting IDs and next-step hints in command output.
- Add a concise `pihole doctor` mode to check config health, file permissions, DNS reachability, and common anti-patterns.
- Offer safer defaults messaging for privacy-sensitive environments (e.g., recommended upstream + DNSSEC profile).

---

## 5) White-hat / penetration-tester perspective

### Threat surfaces to prioritize
- Installer/update path integrity and downgrade protection.
- Script parsing boundaries (key/value file ingestion and substitution helpers).
- Privileged file writes and permissions drift over upgrades.
- Local privilege escalation via weak ownership/perms on executable scripts or config files.

### Security test plan candidates
- Fuzz key/value loaders for malformed lines, overlong values, and control characters.
- Permission tampering tests for `/etc/pihole` and runtime artifacts.
- Validate behavior when PID files and environment values are adversarial.
- Add regression tests for previously disclosed shell-related advisories.

---

## Suggested services, methods, and tooling
- **SAST**: Semgrep + ShellCheck in CI (fast and high signal for shell repos).
- **Supply chain**: Sigstore/cosign attestations for release artifacts and optional installer verification flow.
- **Secret/permission scanning**: gitleaks + custom permission checks in test containers.
- **Performance**: hyperfine benchmarks integrated into scheduled CI (nightly).
- **Threat modeling**: lightweight STRIDE worksheet per major script area.

---

## Ethical, privacy, and compliance considerations
- Preserve privacy-by-default messaging and offer explicit data-retention guidance.
- Minimize telemetry assumptions and clearly disclose any outbound calls in install/update flows.
- Include guidance for regulated environments:
  - immutable infra patterns,
  - configuration auditing,
  - retention constraints,
  - secure DNS policy baselines.

---

## Quick wins (first 30 days)
1. Remove or isolate remaining `eval` usage in `loadVersionFile`.
2. Add key validation guard to `addOrEditKeyValPair`.
3. Introduce `SECURITY.md` with threat model and disclosure process pointers.
4. Add CI shell hardening checks and simple performance baseline commands.
5. Improve top 5 installer/CLI error messages with actionable remediation text.
