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
   missing data, not a plateau); adherence by block and by week (prescribed vs logged, and when it changed);
   prescribed vs actual RPE; key lifts with the load / e1RM trend and the PRs with their dates; the current
   programme and where the client is in it, then what the next 30 days hold (overview.outlook:
   programmingLeftDays, needsNewProgram, trainingScheduled and predictedCycle when present), the client's
   training lens (overview.lens) when present: judge the programming and the recommendations through it;
   body weight trend and the date of the last weigh-in; nutrition adherence; check-in answers and
   readiness; the flags the overview returned.
6. End with a section "What changed and when": the two or three turning points in the data, each with its
   date and one recommendation.

Loads from the connector are kilograms as MSB stores them; client.units is the client's display preference. When you quote a load you read from the connector, give kg and add the converted figure only when the client's units are imperial, saying so. If a dataset is empty, write "no data" rather than guessing.
