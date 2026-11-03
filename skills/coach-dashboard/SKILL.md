---
name: coach-dashboard
description: "One-page dashboard for a client: headline numbers, current programme, adherence by week, key lifts and PR tiles, flags and next steps."
---
`<client>` below is the client the coach named when invoking this skill or in their message.
If no client is named, ask which client before calling any tool.

Build me a one-page coaching dashboard for <client>.

Steps:
1. Call msb_list_clients with include_inactive=true to get <client>'s id, status, units and their
   currentProgram (name and week when present) as the dashboard shows it.
2. Call msb_get_client_data with dataset=overview for the last 6 months (from set to the date 6 months before
   today as YYYY-MM-DD; the connector may shorten it by a day or two and say so in range.note, which is not a
   problem to report), then dataset=training with detail=weekly and dataset=metrics for the same range, then
   dataset=programs and dataset=prs (as of today, the default 'to').
3. Produce a single page (a rendered visual page if you can create one, otherwise compact Markdown with tables) with:
   - a header with the client's name, status and units;
   - four headline numbers: adherence %, sessions logged / prescribed, latest body weight with the change over
     the range, days since the last session;
   - a programme tile: the current programme's name and sessions per week from the overview, the week from
     msb_list_clients' currentProgram, and programmingLeftDays from the overview when present;
   - a PR tile: squat / bench / deadlift best (1RM or e1RM, with the basis and date) and the total when prs.sbd
     has them, otherwise the best e1RM of the top three key lifts;
   - adherence by week as a chart or table;
   - key lifts with the trend per lift;
   - a notes section with the connector's flags, meta.coverage and the latest check-in;
   - three next steps for me.

Loads from the connector are kilograms as MSB stores them; client.units is the client's display preference. When you quote a load you read from the connector, give kg and add the converted figure only when the client's units are imperial, saying so. Use only what the tools returned and mark missing data as "no data". If you render a visual page,
use the MSB green #5bc500 as the accent colour on a dark background.
