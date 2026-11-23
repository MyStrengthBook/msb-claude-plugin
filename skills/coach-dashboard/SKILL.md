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
   - a header with the client's name, status, units and last login (client.lastLoginAt);
   - four headline numbers: adherence %, sessions logged / prescribed, latest body weight with the change over
     the range, days since the last session;
   - a programme tile: the current programme's name and sessions per week from the overview, the week from
     msb_list_clients' currentProgram, and programmingLeftDays from the overview when present;
   - a PR tile: squat / bench / deadlift best (1RM or e1RM, with the basis and date) and the total when prs.sbd
     has them, otherwise the best e1RM of the top three key lifts;
   - adherence by week as a chart or table;
   - key lifts with the trend per lift;
   - a notes section with the connector's flags, meta.coverage, the latest check-in and the assigned habits
     with their compliance (a habit with targetWarning only under a check of its target for me in MSB);
   - three next steps for me.

Loads from the connector are kilograms as MSB stores them; client.units is the client's display preference. When you quote a load, e1RM, volume or body weight you read from the connector, give kg for a metric client; for an imperial client, give every load, e1RM, volume and body weight in lb (kg × 2.20426, to one decimal at most) with the kg figure in brackets. Use only what the tools returned and mark missing data as "no data". If you render a visual page,
use the MSB green #5bc500 as the accent colour on a dark background.
Keep a neutral, professional tone: report what the data shows and what to ask or do next, and state facts about the training plainly ("one session a week was prescribed, none was logged"), never as a verdict on the client's commitment or on how demanding the plan is. Treat the client as a real client, never as a test, demo or placeholder account, whatever their name or however little they have programmed. Leave my own programme housekeeping out (drafts the client has not received, programmes that share a name, deleting or tidying up programmes) unless I ask about it.
When you suggest what the coach should do about a client who is going quiet or has gone more than 7 days without a session, start with a re-engagement message that checks their readiness and what is getting in the way, never a new or next programme first, even when their block is ending or nothing is scheduled. This is about your own suggestions: when the coach asks for a programme for such a client, do not put a re-engagement step in its way.
