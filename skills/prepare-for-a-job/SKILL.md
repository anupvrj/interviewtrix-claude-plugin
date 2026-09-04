---
name: prepare-for-a-job
description: Use when the user pastes a job description or asks to get interview-ready, prepare for a company or role, check their Interview Trix plan or remaining mock-interview credits, or wants ATS feedback plus a mock interview. Also use for "can I start an interview" and "what plan am I on". For building or retargeting a resume after role research, use manage-resumes. Do not use for PDF-only, resume import-only, or past interview reports.
---

# Prepare for a job on Interview Trix

Help the signed-in user get ready for a specific job using Interview Trix tools. The live interview always happens on Interview Trix, never inside this chat.

## When to stop immediately

- They ask to delete an account, wipe resumes, email a recruiter, or post a resume publicly: refuse. Interview Trix cannot do that.
- They want you to conduct the full mock interview in this chat: refuse the live Q&A. Create the interview with tools, then send the join link.

## Steps

1. Call `get_entitlements`. Summarize plan, remaining credits, and whether AI mock interviews and ATS are allowed. If they only asked about plan or credits, stop after this step.
2. If they shared a job description (JD), keep it for ATS. If they want ATS or a mock and there is no JD, ask for the JD or target role before creating an interview.
3. Call `list_resumes`. If they need a resume for this role:
   - Call `start_resume_flow` (or ask one path question): retarget saved resume, brand-new from scratch, upload file, or merge attachment into saved resume — follow `manage-resumes`.
   - If none exist and they chose from scratch: `list_resume_templates`, then `start_resume_intake` if facts are missing, then `create_resume_draft` with `creationMode: "fresh"` and `factsProvenance: "intake"`.
   - If optimizing a saved resume: `create_resume_draft` with `creationMode: "retarget"`, `retargetDepth: "deep"`, `sourceResumeId`, `targetRole`, and `jobDescription` (50+ chars).
   - In voice: map plain requests (“add skills”, “give me the link”) to tools — see **manage-resumes** voice section. Never ask the user to name tools or paste `resumeId`.
   - Reuse contact details already collected in this chat; do not re-ask header unless missing.
   - Do not start a mock yet.
4. If they want content-level advice (rewrite bullets, match keywords), call `get_resume` with that `resumeId`. Do not call `get_resume_pdf` unless they asked for a file.
5. If ATS is in scope (they pasted a JD or asked to score), call `score_resume_ats` with `resumeId` and `jobDescription`. Report the score, top issues, and the ATS editor link. On `upgrade_required`, show the upgrade URL and stop the interview step.
6. Ask before creating a mock. If they agree and mocks are allowed with enough credits, call `initialize_mock_interview` with `role` (from the JD or user), optional `targetCompany`, `jobDescription`, `resumeId`, and duration 15 unless they have Tech Pro or Enterprise and asked for 30 minutes. Language `en` unless they asked for Hindi (`hi`).
7. Give the interview join URL. State clearly: start the mock on Interview Trix; this chat cannot run the live interview.

## Output

- Plan and credits in one short line when relevant.
- Which resume you used (`title` + `resumeId`).
- ATS score and editor link when scored.
- Interview join link only after a successful `initialize_mock_interview`.
- On `resume_not_found`, call `list_resumes` and ask which one to use.
