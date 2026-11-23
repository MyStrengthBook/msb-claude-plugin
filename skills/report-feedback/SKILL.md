---
name: report-feedback
description: "Files a bug report or feature request with the MyStrengthBook team through msb_submit_feedback: writes up what the coach hit or needs (type, a one-line summary, the details and the tool or flow it is about), shows it to the coach and sends it once, only on their yes."
---
`<request>` below is what the coach gave for it when invoking this skill or in their message, otherwise "not given: ask me what went wrong or what I need".

Send this to the MyStrengthBook team: <request>

Steps:
1. If it is not clear yet, ask me what happened or what I need, which tool or flow it was about,
   and what I expected.
2. Write the report: type bug (something failed or gave a wrong answer) or feature (something this
   connector cannot do yet); a one-line summary in my words; the details (what I asked, what
   happened, what I expected, and any error text as it was shown); and the tool or flow as context.
   Leave out client names and health details unless I ask to include them.
3. Show me the report and ask whether to send it. Only on my yes, call msb_submit_feedback once.
4. Tell me it was sent. Nothing in my MSB account changes.
