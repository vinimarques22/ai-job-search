# /outcome - Record the Result of an Application

You are recording what happened to a job application. The data lands in the main tracker:
- `documents/job_tracker.md` - the centralized Markdown table for tracking status.

Follow these steps **in order**.

---

## Step 0: Parse Input

`$ARGUMENTS` may contain:
- Nothing → list open applications from `documents/job_tracker.md` and ask which one to update.
- A company name (optionally with a role), e.g. `/outcome acme` → target that application.

---

## Step 1: Load State and Identify the Application

1. Read `documents/job_tracker.md`. The table has the following columns:
   `| Data | Empresa | Vaga | Plataforma (LinkedIn/Gupy) | Status (Identificada / Aplicada / Não Aplicada / Entrevista / Concluída) | Notas |`
2. **With an argument:** Match rows case-insensitively on Empresa (and Vaga, if given). 
   - One match → proceed.
   - Several → list them and ask.
   - None → the application was made outside the workflow. Collect Empresa, Vaga, Data, Plataforma, and Status from the user and add a new row to the table.
3. **Without an argument:** List all rows whose status is not final (e.g., anything not "Concluída" or "Não Aplicada") and ask which to update.

---

## Step 2: Collect What Happened

Ask the user what happened, then update the Status.
Valid Status values:
- `Identificada`
- `Aplicada`
- `Não Aplicada`
- `Entrevista`
- `Concluída`

Also collect, without interrogating - one or two open questions are enough:
- Dates for the stages reached.
- Any feedback received or notes to add to the `Notas` column.

---

## Step 3: Update the Tracker

Update the matched row in `documents/job_tracker.md`. 
- **CRITICAL:** Ensure you read the current Markdown table, update the specific cell (e.g., Status or Notas), and write the table back cleanly. Do not break the Markdown layout. Ensure pipe `|` alignment is maintained.
- Append a short dated note to the `Notas` column if new information is provided.

---

## Step 4: Confirm

Summarize what was recorded:

> **Outcome recorded for <Vaga> at <Empresa>.**
>
> - Tracker `documents/job_tracker.md` updated: Status → <new status>
> - Notas added: <what was added>
