# Interview Trix — Claude Plugin

## What this plugin does

Interview Trix helps you build ATS-ready resumes and practice AI mock interviews. This plugin connects Claude to your Interview Trix account so you can list, create, and retarget resumes, run ATS scoring against a job description, start mock interviews, and review your past interview reports — all from chat, with deep links back into the Interview Trix web app.

## What's included

- **manage-resumes** — List, open, import, create, retarget, or update resumes and get an editor link (or PDF).
- **prepare-for-a-job** — Get interview-ready for a role: check your plan/credits, tailor a resume, run ATS, and set up a mock.
- **score-resume-for-a-job** — Run ATS scoring and keyword-gap analysis against a job description.
- **start-mock-interview** — Create a 15 or 30 minute AI mock interview and get a join link.
- **review-interview-results** — Review your past interviews, scores, strengths, and detailed reports.
- **Interview Trix MCP connector** — Remote MCP server at `https://mcp.interviewtrix.com/mcp` that powers all of the skills above.

## Install

1. Open **Customize → Plugins → Browse plugins**.
2. Search for **`interviewtrix`**.
3. Click **Install**.

Claude Code users can alternatively run `/plugin install interviewtrix` from the CLI.

## First-time setup

The first time a skill calls an Interview Trix tool, Claude will prompt you to **Connect**. Click it to authorize `https://mcp.interviewtrix.com/mcp` via OAuth using your Interview Trix login. No API keys or tokens are stored in this plugin.

## Usage examples

- **manage-resumes:** "List my Interview Trix resumes." / "Create a new resume from scratch using the Classic template." / "Add Docker and Kubernetes to my skills on the resume we just made."
- **prepare-for-a-job:** "Here's a job description — help me get interview-ready." / "What plan am I on and how many mock credits do I have left?"
- **score-resume-for-a-job:** "Score my resume for a Senior Backend Engineer role that needs Node.js, AWS, and system design." / "Which keywords am I missing for this JD?"
- **start-mock-interview:** "Start a 15-minute mock interview for Senior Backend Engineer using that resume."
- **review-interview-results:** "How did I do on my last interview?" / "Show me the report for intv_..."

## Support / contact

Questions or issues: [info@interviewtrix.com](mailto:info@interviewtrix.com) · [interviewtrix.com](https://interviewtrix.com)
