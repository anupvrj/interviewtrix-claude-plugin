---
name: score-resume-for-a-job
description: Use when the user wants an ATS score, keyword gaps, or to tailor a resume to a job description. Triggers include "ATS", "score my resume", "how does this match this JD", "keywords missing". Do not use for creating a mock interview, importing a new resume, or PDF download unless they also asked for those separately.
---

# Score a resume against a job

Run Interview Trix ATS on a resume the user already owns. Do not invent an ATS score.

## Inputs

- A resume: `res_…` id, a title, or "latest" / "my resume" (then `list_resumes`).
- A job description or target role. If they asked to score against a job and there is no JD, ask for the JD. You may still score without a JD if they only want a general ATS check.

## Steps

1. If `resumeId` is missing, call `list_resumes`. Use the named resume, else the most recently updated. If the list is empty, follow `manage-resumes` intake in chat — do not invent an ATS score or call `create_resume_draft` empty.
2. Call `score_resume_ats` with `resumeId` and `jobDescription` when you have a JD.
3. Report the numeric score, up to a few top issues (fail/warn), and the ATS editor link from the tool.
4. Only call `get_resume` if they also want specific rewrite suggestions from the actual bullets. Do not dump the full resume unless they asked.
5. Do not call `initialize_mock_interview` unless they clearly asked to start a mock after scoring. If they did, follow `start-mock-interview` (credits first).

## Errors

- `resume_not_found`: list resumes and ask which id to use. Never guess another user's resume.
- `upgrade_required` or ATS not on the plan: show the upgrade URL; do not fake a score.
- Do not email the report or post it publicly.

## Output

Plain-language score, what to fix first, and the Interview Trix ATS link. Mention `hasPdf` only if they asked for a file (then use `get_resume_pdf`).
