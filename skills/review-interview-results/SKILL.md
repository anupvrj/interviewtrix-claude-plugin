---
name: review-interview-results
description: Use when the user asks about past Interview Trix mock interviews, overall score, strengths, improvements, or a detailed report. Triggers include "how did I do", "my interviews", "interview report", "intv_" id. Do not use to start a new mock, score a resume, or download a resume PDF.
---

# Review interview results

Show the user's own Interview Trix interview history and reports. Do not invent scores.

## Steps

1. If they did not pass `interviewId` (`intv_…`), call `list_interviews`. Summarize role, status, date, and score when present. Offer the report or join link from the tool.
2. For a completed interview they care about, call `get_interview_report` with that `interviewId`.
3. Summarize overall score, category scores, strengths, and improvements in plain language. Include the full report link from the tool.
4. If the interview is not completed, say the report is not ready. Point them at the join/resume link from `list_interviews` if they still need to finish on Interview Trix.

## Errors

- `interview_not_found`: it is not in their account. List interviews; do not probe other users.
- `report_not_ready`: they must finish the mock on Interview Trix first.
- `upgrade_required` (detailed reports): show the upgrade URL; you may still list interviews and basic scores from `list_interviews`.

## Refusals

Do not start a new mock here unless they explicitly ask (then `start-mock-interview`). Do not email the report or post it publicly.
