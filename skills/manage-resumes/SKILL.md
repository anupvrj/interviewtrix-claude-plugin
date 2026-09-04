---
name: manage-resumes
description: Use when the user wants to list Interview Trix resumes, open or inspect one, import or create a resume draft from chat, pick a template, collect intake, optimize or retarget, fix the same resume in place, or get a PDF. After they pick a template name (Meridian) or id, immediately call create_resume_draft — do not only say it is selected. Never ask for a screenshot. For ATS after create, offer score_resume_ats when the user agrees. Do not use for starting a mock or interview reports unless they ask separately.
---

# Manage Interview Trix resumes

Use Interview Trix resume tools only. Never invent resume content the user did not provide. Never email, publish, or delete resumes.

Keep calling tools until a resume exists and you return the Interview Trix editor link. Do not stop at “template selected”.

After any successful `create_resume_draft` or `update_resume_draft`, treat the returned `resumeId` as the **active resume for this chat** until the user picks another or asks for a new document.

**Never ask the user to call tools by name, paste a `resumeId`, or type MCP parameters.** They speak in plain language; you choose the tool and pass the active `resumeId` from the last successful create/update in this chat (or resolve via `list_resumes` if unclear).

## What the user says → what you do

The user does **not** know tool names. Map their words to actions:

| User says (text or voice) | You do |
|---------------------------|--------|
| “Add Node.js, TypeScript, MongoDB to my skills” / “update skills with…” | `update_resume_draft` on the **active `resumeId`** with `skills` (+ align last two `experience` bullets and `projects` if they asked to match) |
| “Fix the title to Forward AI Engineer” / “wrong subtitle” | `update_resume_draft` with `personalInfo.jobTitle` (+ `targetRole` if set) on active id |
| “Update the resume you just made” / “change this one” | `update_resume_draft` on active id — **never** `create_resume_draft` again |
| “Give me the link” / “where is my resume” (especially in voice) | Read **`Editor link (copy or open):`** and `voiceSummary.speakableUrl` from the **last successful** create/update tool result; if none in this chat, `list_resumes` and offer the latest or ask which title |
| “Create a new resume for [role]” | `start_resume_flow` → path → template → create (not silent retarget) |
| “Use my saved resume for this role” | `list_resumes` → `get_resume` → `create_resume_draft` with `creationMode: "retarget"` |
| “Score it for ATS” / “yes, run ATS” | `score_resume_ats` with active `resumeId` + stored JD from research |

After every update in voice, say in plain language what you did (e.g. “I added Node.js, TypeScript, MongoDB, PostgreSQL, and Pinecone to your skills and aligned your last two roles”) **and** read the full editor URL from the tool output once.

## Presenting choices (text, no inline cards)

This client does **not** render Interview Trix inline widget cards or thumbnails. Never say a picker, card, buttons, or thumbnails are "above", "shown", or "right above" — nothing visual renders here. Instead:

- When a tool returns choices (templates, the four creation paths, interview durations), present them **yourself as a short plain-text list of names** and ask the user to reply with one.
- Read the template names from the `list_resume_templates` tool result (its `templates` array) and list those names. Do not ask the user to type or paste an id.
- Still run read-only tools (`list_resumes` / `get_resume`) first, then call the one action tool for the turn.
- After a create/update, give the editor link as a normal markdown link in your text (there is no card to hold it).

## Start here (mandatory when creating or importing)

1. Call `start_resume_flow` or `list_resumes` when the user wants a **new** or **imported** resume.
2. Ask **one** path question in plain text:
   - **Retarget saved resume** — `get_resume` → `create_resume_draft` with `creationMode: "retarget"`, `sourceResumeId`, `targetRole`, `jobDescription`. Facts come from the saved resume only; do not send rewritten employment stubs.
   - **Brand-new from scratch** — `creationMode: "fresh"`. Template → intake or attachment. **Do not** reuse employment from chat context or a prior resume unless the user explicitly provides it again.
   - **Upload / attach a file** — extract text in chat → `import_resume_text` → `create_resume_draft` with `creationMode: "fresh"` (or `merge_attachment` if enhancing a saved resume).
   - **Enhance saved resume with new attachment** — `import_resume_text` or extracted fields → `create_resume_draft` with `creationMode: "merge_attachment"` and `sourceResumeId`.
3. Reuse header/contact from earlier in the **same chat** when starting a new role document — do not re-ask full name, email, and phone unless missing.

## Fresh resume facts (anti-copy guard)

On `creationMode: "fresh"`, if the payload includes employment or education you **must** set `factsProvenance`:

| Value | When |
|-------|------|
| `intake` | After `start_resume_intake` collected the facts |
| `import` | After `import_resume_text` from an attachment |
| `chat_confirmed` | User **explicitly** said to use facts they already stated in this chat (e.g. “use what I told you”) |

Without `factsProvenance`, Interview Trix rejects the create. Never silently copy a saved IT resume or prior employment from chat.

## Voice / hands-free

In voice the user cannot see links and will **not** say tool names — only things like “add these skills”, “update my resume”, or “give me the link”.

1. **Map plain speech to tools** (see table above). Use the **active `resumeId`** from the last successful create/update in this chat.
2. **Always call the tool** in the same turn before saying the resume was updated. Never narrate a change without a tool result.
3. Read the **`Editor link (copy or open):`** line and `data.voiceSummary.speakableUrl` from the tool result verbatim. Never invent, truncate, or paraphrase URLs.
4. When they ask for the link: give the **full HTTPS URL once**, then offer to repeat it. Optionally spell the resume id using `voiceSummary.resumeIdSlow`.
5. After updates, briefly confirm in everyday language what changed, then the link. Optionally call `get_resume` to verify skills/sections before confirming.
6. **Never** tell the user to “call update_resume_draft” or paste `res_…` — that is your job.

### Voice examples (user → you)

- **User:** “Add Node, TypeScript, Mongo, Postgres, and Pinecone to my skills and match my last two jobs.”  
  **You:** call `update_resume_draft` with active `resumeId`, updated `skills`, and retargeted bullets for the two most recent experience entries; read back the editor link.

- **User:** “Can I get the link?”  
  **You:** read `Editor link (copy or open): …` from the last tool result; if missing, `list_resumes` and read the link for the active or most recent resume.

- **User:** “Did that save?”  
  **You:** if you have not called a tool this turn, call `get_resume` on the active id and confirm the section changed; do not guess.

## Retarget quality (deep rewrite)

When retargeting with `creationMode: "retarget"`:

1. Pass **`jobDescription`** (50+ chars) with research keywords, responsibilities, and must-have skills.
2. Use **`retargetDepth: "deep"`** (default) — Interview Trix rewrites the **last two jobs** with role-specific bullets and aligns projects, skills, and certifications to the same story.
3. Do not send rewritten `experience` stubs in the payload; facts come from the saved resume only.
4. Fix title/subtitle on the **same** draft with `update_resume_draft`, not another create.

## Edit vs fork (same resume vs new draft)

| Situation | Tool |
|-----------|------|
| Fix header, title, typo, email, LinkedIn, one bullet, wrong subtitle | `update_resume_draft` on the **same `resumeId`** |
| User says “fix the resume you just created” / “update this one” | `update_resume_draft` with the active `resumeId` |
| Full role retarget, new template layout, merge new PDF, separate copy | `create_resume_draft` (new draft; original unchanged when `sourceResumeId` set) |
| Switch career domain with mostly new employment | `create_resume_draft` fresh — not in-place patch |

Do **not** recreate a resume when `update_resume_draft` is enough.

## Templates

- There are **17** templates. Do not say "50+".
- **Never pick a template yourself** (do not default to Harvard or Classic).
- Call `list_resume_templates` when they need to pick a layout, then list the returned template names as plain text for them to choose from. If they want to **change** the template, call `list_resume_templates` again and list the names again.
- **Never** ask them to type or paste a template id. A name like Classic, Harvard, or Meridian is enough.
- **Never** ask them to screenshot Interview Trix.
- When they reply with a template — **immediately call `create_resume_draft`** with that `templateId` and the facts you already have. Then return the editor link.
- If `create_resume_draft` returns `needsTemplate`, list the names from JSON, wait for a pick, then **call create again**. Do not only acknowledge.

## Pick a resume

- Prefer `resumeId` when they give `res_…`.
- If they give a title/name, pass it to `get_resume_pdf` as `name`, or find it via `list_resumes` and then use the id.
- If neither id nor name is given and they want one resume, call `list_resumes` first.

## Steps by intent

### List

1. Call `list_resumes`.
2. Show title, `resumeId`, ATS score if present, whether `hasPdf` is true, and the editor links.
3. That list is **titles and ids only**. Call `get_resume` for jobs, education, and skills.

### Read content

1. Resolve `resumeId`.
2. Call `get_resume`. Use the JSON for rewriting or Q&A.

### New resume (template, then create)

1. Confirm path (see **Start here**). Use `creationMode: "fresh"` unless retargeting or merging.
2. **Template first, always.** Call `list_resume_templates` and wait for the user's pick before intake. `start_resume_intake` **requires** a chosen `templateId` — calling it without one returns the template picker, not the form.
3. When they pick, call `create_resume_draft` with `templateId`, `factsProvenance`, and collected facts.
4. If facts are missing, call `start_resume_intake` **with the chosen `templateId`** (and optional `prefill` from earlier in chat). Never call `start_resume_intake` before a template is selected. Ask only the next section in text clients. Intake sets `factsProvenance: intake`.
5. Pass `jobDescription` + `targetRole` when they researched a role.

If `create_resume_draft` returns `resume_intake_required`, call `start_resume_intake` (or ask only `nextSection`), then create again.

On resume-limit / `upgrade_required`, show the upgrade URL. Do not retry create.

### After researching a target role

Research is the **target**, not a biography. Facts still come from an attachment, a saved resume, or intake.

1. Fresh from file/intake: `creationMode: "fresh"`, no `sourceResumeId`, with `jobDescription` + `targetRole` + `templateId`.
2. Optimize a saved resume: `creationMode: "retarget"`, `retargetDepth: "deep"`, `get_resume`, then `create_resume_draft` with `sourceResumeId` + `jobDescription` (50+ chars) + `targetRole`.
3. Attachment + enhance saved resume: `creationMode: "merge_attachment"` with `sourceResumeId`.

### Optimize / retarget without new research

1. `list_resumes` / `get_resume`.
2. `create_resume_draft` with `sourceResumeId` and `creationMode: "retarget"`. Overlay only rewritten fields when JD is absent; with JD, facts stay from source.
3. Return the new editor link. Original unchanged.

### Fix / update same resume

The user says things like “add these skills”, “fix the title”, “update my resume” — **not** tool names.

1. Use the **active `resumeId`** from the last create/update in this chat (or `list_resumes` if ambiguous).
2. Call `update_resume_draft` with only the fields that changed.
3. Return the **same** editor link in plain language + full URL (required in voice). Do not create a new resume for header/title/skills/contact fixes.

### After create — offer next steps

When `create_resume_draft` or `update_resume_draft` succeeds, read the **Editor link** from the tool output (required in voice). Mention the editor link and **offer** ATS scoring (`score_resume_ats`) if they researched a role or pasted a JD. Do not auto-run ATS without the user agreeing. Do not claim success without a tool result in the current turn.

### PDF

1. Only if they asked for a PDF.
2. `get_resume_pdf` with `resumeId` or `name`.
3. Give the short-lived link and expiry.

## Refusals

Do not delete resumes, change another user's data, or send the resume to LinkedIn/email.
