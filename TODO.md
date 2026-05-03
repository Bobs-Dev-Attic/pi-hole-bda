# TODO Backlog (Prioritized)

## P0 - Critical security and integrity
- [ ] Replace `eval` assignment in `advanced/Scripts/utils.sh:loadVersionFile()` with a non-eval mapping strategy.
- [ ] Validate key format in `addOrEditKeyValPair()` before regex operations.
- [ ] Add permission/ownership verification checks for critical paths (`/etc/pihole`, `/opt/pihole`, runtime PID/config files).
- [ ] Add regression tests for adversarial versions file and PID file inputs.

## P1 - High-value reliability and UX
- [ ] Add actionable remediation text for common install/update failures.
- [ ] Implement a non-interactive health command (`pihole doctor`) with structured output.
- [ ] Create centralized compatibility/support matrix markdown for distros and versions.
- [ ] Add deterministic error codes for automation users.

## P2 - Performance and memory efficiency
- [ ] Benchmark high-frequency CLI and gravity workflows on low-resource profiles.
- [ ] Reduce unnecessary subprocess calls in hot script paths.
- [ ] Add basic runtime profiling docs and benchmark targets for contributors.

## P3 - Process, governance, and contributor experience
- [x] Add security-focused CI checks (ShellCheck profile + Semgrep rules).
- [x] Add supply-chain verification guidance and optional signature/checksum verification mode.
- [x] Publish script interaction map to reduce onboarding time.
- [x] Add AI-agent quick-context docs to reduce token usage and duplicate analysis.
