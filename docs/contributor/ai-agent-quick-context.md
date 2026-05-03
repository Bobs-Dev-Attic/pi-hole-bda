# AI Agent Quick Context

This compact context is intended to reduce duplicate exploration for automation agents.

## Fast orientation

- Main CLI entrypoint: `pihole`
- Installer: `automated install/basic-install.sh`
- Shared helpers: `advanced/Scripts/utils.sh`
- Tests: `test/`

## High-risk edit zones

- Writes under `/etc/pihole` and `/opt/pihole`
- Config parsing/mutation helpers
- Installer/update network fetch logic

## Minimal command pattern

- `rg --files`
- `rg "pattern" <path>`
- `sed -n 'start,endp' <file>`

## Change hygiene checklist

- Keep shell quoting strict.
- Avoid introducing `eval` in new code.
- Add regression coverage for malformed/hostile inputs where practical.
- Document security and operational impact in markdown near code changes.
