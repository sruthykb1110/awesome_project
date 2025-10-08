# awesome_project

## 1. Environment Details
- **MySQL Version**: `mysql  Ver 8.0.43 for Linux on x86_64 (MySQL Community Server - GPL)`
- **sdm Version**: `sdm 0.6.4`

---

## 2. How `sdm` Works (Brief, in My Own Words)

The **schema-data-migration (sdm)** tool combines **Skeema** (for schema migrations) with its own system for **data migrations**:

- **Schema migration (Skeema):**  
  Edit `schema/*.sql` → run `sdm make-schema <name>` to create a migration plan → `sdm migrate <env>` applies it using `skeema push`.  

- **Data migration:**  
  `sdm make-data <name> sql|python|typescript|shell` creates a plan with forward/backward steps.  
  `sdm migrate` runs the forward step, `sdm rollback` reverses it.  

- **History & logs:**  
  `_migration_history` stores applied versions, `_migration_history_log` records migrate/rollback/fix operations.

---

## 3. Commands Executed (Step-by-Step)

1. **Prepare databases**
   ```bash
   mysql -u root -p -e "CREATE DATABASE awesome_db;"
   mysql -u root -p awesome_db < user.sql
   ```

2. **Initialize project**
   ```bash
   sdm init
   ```

3. **Add environment**
   ```bash
   sdm add-environment dev --host mysql-dev --port 3306 --user root --schema awesome_db
   ```

4. **Initial schema migration**
   ```bash
   sdm make-schema init
   sdm migrate dev
   ```

5. **Add schema migrations**
   ```bash
   sdm make-schema add_address_to_user_tab
   sdm migrate dev
   ```

6. **Show migration history**
   ```bash
   sdm history dev
   ```

7. **Data migration**
   ```bash
   sdm make-data seed_user_table sql
   sdm migrate dev
   ```

8. **Rollback**
   ```bash
   sdm rollback dev --target 0002
   ```

---
4. One Challenge & Solution

- **Issue:** During `0003_seed_user_table`, Windows quoting/encoding corrupted column names (`id, ame,  ddress`). Migration failed with a SQL error.  
- **Solution:** Re-saved files as **UTF-8 (no BOM)**, used **single-quoted here-strings** to preserve backticks, and reconciled history with `sdm fix migrate/rollback --fake`. After this, the migration applied successfully and rollback worked.

---
5. Results Verified

- Schema migrations `0000` → `0002` applied successfully.  
- Data migration `0003` inserted a test row, rollback removed it.  
- `_migration_history.csv` and `_migration_history_log.csv` confirm states.  
- Rollback tested — schema returned to earlier version safely.  

---

## 6. Lessons Learned

- Save migration files as **UTF-8** to avoid corruption.  
- Always test **rollback** to ensure reversibility.  
- Use **dry run** (`sdm migrate --dry-run`) before applying changes.  
- Migration history tables are crucial for consistency across environments.

---

7. Files Included

- `0000_init.json`, `0001_add_address_to_user_tab.json`, `0002_add_address_to_user_tab_v2.json`, `0003_seed_user_table.json`  
- `_migration_history.csv`, `_migration_history_log.csv`  
- `user.sql` (base schema)  
- `sdm.log` (execution logs)  
- `README.md` (this documentation)

