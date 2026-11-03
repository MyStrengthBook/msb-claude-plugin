---
name: re-engagement-plan
description: "For a client who went quiet: a short warm message from the coach plus a two-week reintroduction plan built from their last logged loads at deload intensity."
---
`<client>` below is the client the coach named when invoking this skill or in their message.
If no client is named, ask which client before calling any tool.

<client> has gone quiet. Help me bring them back.

Steps:
1. Call msb_list_clients with include_inactive=true and activity_window_days=90 to get <client>'s id, status
   and lastCompletedDate (the roster only reports a last session that falls inside that window). If
   lastCompletedDate is still missing or activity is null, call msb_get_client_data with dataset=training,
   detail=weekly and from set to the date 12 months before today as YYYY-MM-DD, and take summary.lastLoggedDate
   (or meta.coverage.lastRecordDate) as the date of their last session. If neither is present, there is no
   logged session in the last 12 months: skip step 2 (there is no load data) and write the message and the plan
   in step 3 without loads, saying so explicitly.
2. If you found a last-session date, call msb_get_client_data with dataset=training and detail=sessions with 'to'
   set to that date (YYYY-MM-DD) and from set to 8 weeks before it, so we know the last loads they actually
   lifted; then dataset=metrics for the same range for body weight.
3. Write two things:
   a) A short, warm message from me to <client>: under 80 words, first person, no guilt, one clear ask
      (pick a day this week). Mention one specific thing from their data that shows I noticed.
   b) A two-week reintroduction plan: 2 or 3 sessions per week built from their last logged loads at deload
      intensity (about 70-80% of the last working weights). Show the numbers per exercise and the reference
      weight you started from. Keep sets and reps conservative in week 1 and add one step in week 2.

Loads from the connector are kilograms as MSB stores them; client.units is the client's display preference. When you quote a load you read from the connector, give kg and add the converted figure only when the client's units are imperial, saying so. State every assumption you make. I send the message and decide on the programme myself;
when the msb_preview_program tool is available, offer to draft the plan as an import JSON (following
msb://import-rules) for me to preview and approve before anything is saved.
