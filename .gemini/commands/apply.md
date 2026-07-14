# /apply - Drafter-Reviewer Job Application Workflow

You are orchestrating a two-agent job application workflow. The job posting is provided below as `$ARGUMENTS` (either a URL or pasted text).

Follow these steps **exactly in order**. Do not skip steps.

**Execution Constraints & Token-efficiency rules:**
- **NO AUTO-APPLY:** Do not execute any downstream automation scripts for direct browser button application subroutines. The system must act as a curation and document drafting platform only.
- **Artifacts:** Output exactly TWO fully localized (PT-BR) assets per loop: 
  1. A tailored LaTeX `currículo` file compiled using `lualatex` (max 2 pages). 
  2. A clean Markdown structural text block containing optimized Q&A text answers mapped precisely to "Adicionais" / screening / easy apply questions.
  **Do NOT generate any cover letters.**
- Never re-Read a file whose contents are already in your context from an earlier step.
- When dispatching the reviewer agent, pass draft content **inline in the agent prompt** rather than asking the agent to Read files you already have in memory.
- Run the full verification checklist exactly once, at the end.
- Step 5 (compile and inspect PDFs) is mandatory and non-skippable for the CV.

---

## Step 0: Parse Input

- If `$ARGUMENTS` looks like a URL, use `WebFetch` to retrieve the job posting content.
- If it is pasted text, use it directly.
- Extract: **company name**, **role title**, **department** (if mentioned), **location**, **screening questions / Adicionais**.
- Store these for use throughout the workflow.
- **Gupy Exception Logic:** If the source URL belongs to `gupy.io`, note this down. For Gupy, do NOT optimize the core LaTeX document files aggressively beyond a structural keyword check, since the user utilizes a static profile on that platform. Focus heavily on providing the "Informações Adicionais/Respostas" text field optimization blocks.

---

## Step 1: DRAFTER - Evaluate Fit

Read the evaluation framework:
- `.gemini/skills/job-application-assistant/04-job-evaluation.md`
- `.gemini/skills/job-application-assistant/01-candidate-profile.md`

Using the framework from `04-job-evaluation.md`, evaluate the job posting against the candidate's profile. Present the evaluation to the user with:

1. **Skills match** - which required/preferred skills match vs. gaps
2. **Experience match** - how work history maps to the role
3. **Behavioral/culture match** - how behavioral profile fits the role/company culture
4. **Overall fit score** and recommendation (strong fit / moderate fit / weak fit)

After presenting the evaluation, ask the user:
> "Should I proceed with drafting the currículo and Q&A text block for this role?"

**If the user says no, stop here.** If yes, continue to Step 2.

---

## Step 2: DRAFTER - Draft CV + Q&A Block

You already have `01-candidate-profile.md` and `04-job-evaluation.md` in context from Step 1. **Do not re-read them.**

Read only the reference files you do not yet have:
- `.gemini/skills/job-application-assistant/03-writing-style.md`
- `.gemini/skills/job-application-assistant/05-cv-templates.md`

### CV (`cv/main_<company>.tex`)
- Always in **Portuguese (PT-BR)**.
- Follow the format from `05-cv-templates.md`.
- Tailor the profile statement and experience bullets to the specific role (unless Gupy Exception applies, then only do structural keyword check).
- Keep to a maximum of 2 pages.

### Q&A Text Block (`documents/applications/qa_<company>_<role>.md`)
- Create a clean Markdown text block containing answers to any identified screening or "easy apply" extra questions found in the posting or provided by the user.
- If no explicit questions are found, create a generic "Informações Adicionais" section highlighting why the candidate is a strong fit.
- Answers should be in **Portuguese (PT-BR)**.
- Any mention of agentic coding or AI tooling must reference **Gemini Agent** by name.

Write both files to disk. Keep the exact text of both drafts in working memory.

---

## Step 3: REVIEWER - Research & Critique

Use the **Agent tool** to spawn a `general-purpose` reviewer agent. Pass the drafts **inline in the prompt**.

Replace `<COMPANY>`, `<ROLE>`, `<INSERT_JOB_POSTING_TEXT_HERE>`, `<INSERT_CV_DRAFT_HERE>`, and `<INSERT_QA_DRAFT_HERE>` with actual values before dispatching.

```
You are a hiring manager proxy reviewing a job application. Your job is to make the application as targeted and compelling as possible.

### 1. Research the Company
Use WebSearch and WebFetch to research the company's culture and values.

### 2. Read Reference Materials
Read these files for content critique:
- `.gemini/skills/job-application-assistant/01-candidate-profile.md`
- `.gemini/skills/job-application-assistant/02-behavioral-profile.md`
- `.gemini/skills/job-application-assistant/03-writing-style.md`
- `.gemini/skills/job-application-assistant/04-job-evaluation.md`

### 3. Drafts to Review
<CV_DRAFT file="cv/main_<COMPANY>.tex">
<INSERT_CV_DRAFT_HERE>
</CV_DRAFT>

<QA_DRAFT file="documents/applications/qa_<COMPANY>_<ROLE>.md">
<INSERT_QA_DRAFT_HERE>
</QA_DRAFT>

### 4. Job Posting
<JOB_POSTING>
<INSERT_JOB_POSTING_TEXT_HERE>
</JOB_POSTING>

### 5. Produce Feedback
Return your feedback in **two parts**:

**Part A — Structured edits:**
A JSON array of concrete edits.
```json
{
  "file": "filename",
  "old_string": "<exact text currently in the draft>",
  "new_string": "<replacement text>",
  "reason": "<rationale>"
}
```

**Part B — Narrative suggestions:**
Prose suggestions for: Missed keywords, Company angles, Action-oriented reframing, Tone/style issues.
**CRITICAL RULE:** Do NOT fabricate skills.

Return Part A and Part B together as a single structured message.
```

---

## Step 4: DRAFTER - Revise Based on Feedback

1. **Apply Part A (structured edits) directly with the Edit tool.** 
2. **Apply Part B (narrative suggestions)** using judgment. Ensure PT-BR localization.
3. **Gupy Exception:** If this is a Gupy role, discard CV edits that overly tailor the content beyond keywords, but heavily apply Q&A edits.

---

## Step 5: DRAFTER - Compile & Inspect PDFs (MANDATORY)

**Never skip this step for the CV.** 

### 5a. Compile

```bash
cd cv && lualatex -interaction=nonstopmode main_<company>.tex
```

If compile fails, fix the error and re-compile until clean.

### 5b. Inspect layout

Read the PDF via the Read tool and verify:
- [ ] Max 2 pages
- [ ] No orphaned `\cventry` titles

### 5c. ATS & keyword verification (CV)
- Extract text with `pdftotext -layout`.
- Verify keyword coverage against the posting. Ensure keywords are in PT-BR.
- Delete the `.txt` file after checking.

### 5d. Clean up build artifacts
Delete `.aux`, `.log`, `.out` files.

---

## Step 6: Present Final Output

Run the full verification checklist from `GEMINI.md`.

### Key Tailoring Decisions
Summarize 3-5 key decisions made to tailor the application.

### Files Created
List the files written:
- `cv/main_<company>.tex`
- `documents/applications/qa_<company>_<role>.md`

Tell the user: "Both files are ready for your review. Open them to check the final output before compiling."

### Next Steps
- **Submitted?** `/outcome <company>` logs it in the `documents/job_tracker.md` tracker.
- **Interview scheduled?** `/interview` builds a stage-specific prep pack.
