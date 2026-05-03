# Script Interaction Map

This map highlights how major scripts in this repository call into each other and which artifacts they affect.

## Entry points

- `pihole` (primary CLI)
  - Sources shared helpers from `advanced/Scripts/utils.sh`.
  - Dispatches high-level commands to scripts in `advanced/Scripts/` (for example `gravity.sh`, API and query helpers).
- `automated install/basic-install.sh` (installer)
  - Drives install/bootstrap flow.
  - Writes runtime configuration under `/etc/pihole` and installs core scripts under `/opt/pihole`.

## Shared helper layer

- `advanced/Scripts/utils.sh`
  - Cross-script helpers (logging, key/value parsing and mutation, environment checks).
  - Used by command and maintenance scripts.

## Operational scripts

- `gravity.sh`
  - Rebuilds gravity domain data and updates blocking state.
- `advanced/Scripts/query.sh`
  - Query/adlist lookup functionality used by CLI query operations.
- `advanced/Scripts/list.sh`
  - Allowlist/denylist and list-management helpers.
- `advanced/Scripts/update.sh` + `advanced/Scripts/updatecheck.sh`
  - Update checks and update execution orchestration.

## Service/runtime integration

- `advanced/Templates/`
  - Service templates (`pihole-FTL.service`, openrc templates), cron, and logrotate artifacts.
- `advanced/Scripts/database_migration/`
  - Schema migration scripts used during upgrades.

## Tests and validation

- `test/`
  - Python-based automated install and cross-distro test scenarios (`tox.*.ini`, distro Dockerfiles).

## Typical flow snapshots

1. **Install path**: `basic-install.sh` -> writes configs and services -> initializes runtime.
2. **CLI command path**: `pihole` -> shared `utils.sh` + targeted script (`query.sh`, `list.sh`, `gravity.sh`, etc.).
3. **Update path**: `pihole` -> `updatecheck.sh`/`update.sh` -> optional DB migrations.
