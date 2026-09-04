---
name: start-mock-interview
description: Use when the user wants to start, create, or practice an AI mock interview on Interview Trix for a role or company. Also use for "can I interview", remaining interview credits, 15 vs 30 minutes, or Hindi vs English mock. Do not use for past reports, ATS-only scoring, or resume PDF.
---

# Start a mock interview

Create a draft AI mock on Interview Trix and send a join link. Do not run the live interview in this chat. Do not pretend to be the interviewer after the link is created.

## Steps

1. Call `get_entitlements`. If AI mock is not allowed or credits are insufficient, explain that, show the upgrade URL when the tool provides one, and stop.
2. Confirm `role` (job title). If missing, ask. Optional: years of `experience`, `targetCompany`, `jobDescription`, `language` (`en` or `hi`).
3. **Ask the user to pick the duration — never choose it yourself and never default to 30.** Call `start_interview_flow` (pass `role`, and `targetCompany`/`jobDescription` if known). On ChatGPT this shows a 15/30 picker widget; on other clients ask one short question ("15 or 30 minutes?"). Wait for their choice. 30 is only offered on Tech Pro/Enterprise. Only skip this step if the user already stated a specific duration in their request.
4. Resolve a resume: `resumeId` they gave, else `list_resumes` (newest or named). If there is no resume, follow `manage-resumes` intake (collect details in chat, then `create_resume_draft`). Do not start the mock until a resume exists.
5. After the user picks a duration, call `initialize_mock_interview` with `role`, that chosen **`duration`**, **`resumeId`** (use the active resume from this chat when they just created one), and optional `jobDescription`, `targetCompany`. **Do not** send `department` or `discipline` unless the user gave an exact allowed value — omitting them is fine.
6. Only say the interview was created after the tool returns `success: true` with `interviewId` and a join URL. If the tool errors, explain the error — do not pretend it worked.
7. In the **same reply**, paste the full join URL from `Join link (copy or open):` and the `interviewId`. Do not tell the user to ask again for the link. The actions widget may also show a Start interview button.
8. Say they must open Interview Trix to take the interview (camera/mic on that site).

## After create

- Do not fire interview questions in this chat.
- If they want history or a score from a finished session, use `review-interview-results` instead.

## Errors

- `upgrade_required`: show upgrade link; do not create the interview.
- `resume_required` / empty resume: send them to create or edit a resume first.
- Never start a mock for someone else's resume id; `resume_not_found` means list theirs and ask.
