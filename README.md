# awesome_project

## Environment details
- MySQL: mysql  Ver 8.0.43 for Linux on x86_64 (MySQL Community Server - GPL)
- sdm: sdm 0.6.4

## How sdm works (brief)
- **Schema migration (Skeema):** Edit `schema/*.sql` to the desired state, then `sdm make-schema <name>` snapshots a JSON plan. `sdm migrate <env>` applies it (Skeema computes and runs the DDL).
- **Data migration:** `sdm make-data <name> sql|python|typescript|shell` creates a plan with forward/backward. `sdm migrate` runs forward; `sdm rollback` runs backward.
- **History & logs:** `_migration_history` tracks applied plans; `_migration_history_log` records migrate/rollback/fix operations.

## One challenge & solution
Windows quoting/encoding corrupted identifiers inside a plan (it showed as `id, ame,ddress`).
**Fix:** saved files as **UTF-8 (no BOM)**, used **single-quoted here-strings** so backticks are literal, and reconciled history with `sdm fix migrate/rollback --fake` when needed.

## Sections completed
1. Prepare databases
2. Initialize project
3. Add environment
4. Migrate
5. Make schema migration plan
6. Show migration history
7. Make data migration plan
8. Rollback
