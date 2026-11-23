---
name: client-review
description: "Deep review of one client over the last N months (up to 12): coverage, adherence by block and by week, RPE, key lifts and PRs, current programme, body weight, nutrition, check-ins, and what changed when."
---
`<client>` below is the client the coach named when invoking this skill or in their message; `<months>` is the number of months they gave (1-12), otherwise 6.
If no client is named, ask which client before calling any tool.

Review my client <client> over the last <months> months.

Steps:
1. Call msb_list_clients with include_inactive=true and include_activity=false to confirm <client> is on my
   roster and to get their id, status and units.
2. Call msb_get_client_data with dataset=training for the full period: from set to the date <months> months
   before today as YYYY-MM-DD, leaving 'to' at its default of today (training may span up to 12 months). Use
   detail=blocks when the period is 3 months or longer, otherwise detail=weekly. When you used blocks, also
   call detail=weekly for the last 8 weeks.
3. Call msb_get_client_data with dataset=overview, then dataset=metrics and dataset=nutrition, for the last
   <months> months, capped at 6 months: the connector shortens longer requests and says so in range.note.
   If the review is longer than that, say that body weight, nutrition and check-ins cover the last 6 months
   only. The overview keeps only the latest three check-ins: call dataset=checkins for the same range for
   the full check-in text (every questionnaire answer in the client's own words, daily habits, client
   notes and coach notes), and quote the answers verbatim rather than summarising them.
4. Call msb_get_client_data with dataset=programs (which programme is current, weeks, sessions per week) and
   dataset=prs (best rep maxes, e1RM and squat/bench/deadlift total as of today, the default 'to').
5. Report, in this order: coverage (meta.coverage: days with data vs days elapsed; say when a flat line is
   missing data, not a plateau); the date of the last logged session (training.summary.lastLoggedDate) next
   to the last login (client.lastLoginAt); adherence by block and by week (prescribed vs logged sessions and
   planned vs completed sets, and when it changed); prescribed vs actual RPE; key lifts with the load / e1RM
   trend and the PRs with their dates; the
   current programme and where the client is in it, then what the next 30 days hold (overview.outlook:
   programmingLeftDays, needsNewProgram, trainingScheduled and predictedCycle when present), the client's
   training lens (overview.lens) when present: judge the programming and the recommendations through it;
   body weight trend and the date of the last weigh-in; nutrition adherence; check-in answers and
   readiness; assigned habits and their compliance (overview.checkins.assignedHabits and habitSummary), a
   habit with targetWarning only under a check of its target for me in MSB;
   the flags the overview returned.
6. End with a section "What changed and when": the two or three turning points in the data, each with its
   date and one recommendation.

Loads from the connector are kilograms as MSB stores them; client.units is the client's display preference. When you quote a load, e1RM, volume or body weight you read from the connector, give kg for a metric client; for an imperial client, give every load, e1RM, volume and body weight in lb (kg × 2.20426, to one decimal at most) with the kg figure in brackets. If a dataset is empty, write "no data" rather than guessing.
Keep a neutral, professional tone: report what the data shows and what to ask or do next, and state facts about the training plainly ("one session a week was prescribed, none was logged"), never as a verdict on the client's commitment or on how demanding the plan is. Treat the client as a real client, never as a test, demo or placeholder account, whatever their name or however little they have programmed. Leave my own programme housekeeping out (drafts the client has not received, programmes that share a name, deleting or tidying up programmes) unless I ask about it.
When you suggest what the coach should do about a client who is going quiet or has gone more than 7 days without a session, start with a re-engagement message that checks their readiness and what is getting in the way, never a new or next programme first, even when their block is ending or nothing is scheduled. This is about your own suggestions: when the coach asks for a programme for such a client, do not put a re-engagement step in its way.
