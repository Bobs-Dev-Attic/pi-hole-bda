# Agent Context Pack (Token-Efficient)

Purpose: reduce repeated repository exploration for coding agents (Codex/Claude Code) while keeping analysis consistent.

## Fast orientation
- Main CLI entrypoint: `pihole`
- Installer: `automated install/basic-install.sh`
- Shared shell helpers: `advanced/Scripts/utils.sh`
- Tests: `test/`

## High-risk edit zones
- Any function that writes to `/etc/pihole` or `/opt/pihole`
- Config parsing helpers (`loadVersionFile`, key/value mutation helpers)
- Update/install fetch logic and repository trust assumptions

## Preferred review sequence
1. `README.md` for user-facing behavior and supported workflows.
2. `pihole` for command routing and privileged operations.
3. `advanced/Scripts/utils.sh` for reusable low-level helpers.
4. Installer script for dependency, environment, and trust model decisions.
5. `test/` for coverage gaps before implementing fixes.

## Minimal command set for future agents
- `rg --files`
- `rg "functionName|pattern" <paths>`
- `sed -n 'start,endp' <file>`

## Change hygiene checklist
- Keep shell quoting strict.
- Avoid introducing new `eval` usage.
- Add/extend tests for utility function behavior and malformed input.
- Document operational/security implications in markdown alongside code changes.

## Output expectations
- Provide prioritized risk summaries (P0-P3).
- Separate confirmed issues from suggested improvements.
- Include exact file/line citations in final response.
