---
name: next-block-suggestion
description: "Reads a year of training by programme block plus programmes, PRs and the plan, summarises what each block did to the key lifts and RPE, and proposes the structure of the next block for the coach to decide on."
---
`<client>` below is the client the coach named when invoking this skill or in their message; `<goal>` is any further text they gave after the client name, otherwise "the goal implied by the client's open goals, upcoming events and current programme".
If no client is named, ask which client before calling any tool.

Suggest the next training block for <client>. Goal for the block: <goal>.

Steps:
1. Call msb_list_clients with include_inactive=true and include_activity=false to get <client>'s id, status
   and units.
2. Call msb_get_client_data with dataset=training, detail=blocks and from set to the date 12 months before
   today as YYYY-MM-DD, leaving 'to' at its default of today. If one lift matters most for the goal, call it
   again with exercise set to that lift's name and detail=weekly for the last 3 months.
3. Call msb_get_client_data with dataset=programs (which programme is current, weeks, sessions per week),
   dataset=prs (best rep maxes and e1RM per lift as of today, the default 'to') and dataset=plan with 'to' set
   to the date 180 days from today as YYYY-MM-DD (upcoming phases, events with daysUntil, goals with daysLeft,
   check-in dates), so that an event within the next 16 weeks is inside the window. Also call dataset=checkins
   for the last 8 weeks and read the questionnaire answers in the client's own words: how the client says
   training feels is evidence for the next block, so quote it, don't just paraphrase it.
4. Summarise what each block did, one line per block: weeks, sessions per week and adherence, sets per muscle
   group when setsByMuscleGroup is present (otherwise say it was omitted), average RPE and RPE delta vs
   prescribed, and for each key lift first → best → last e1RM with the change in %. Say where the data is too
   thin to conclude: low meta.coverage, block adherence under 60%, or fewer than 3 sessions of a lift.
5. Propose the next block: length in weeks (fit it before the next event when one is within 16 weeks),
   sessions per week (what the client actually completed, not what was prescribed), the lifts to keep, change
   or add (keep what progressed, change what stalled, add work for muscle groups with few sets), starting
   intensities as a % of the client's stored 1RM per lift (prs.oneRepMaxes) with the kg next to them (that
   max × the %; the e1RM trend is evidence of current strength, not the % basis, and where no 1RM is stored
   say so: a % there comes out 0 in MSB until I set that 1RM), the weekly progression and when to deload,
   and the one or two numbers to watch during the block.
6. State every assumption explicitly, and separate what the data shows from what you are inferring.

Loads from the connector are kilograms as MSB stores them; client.units is the client's display preference. When you quote a load you read from the connector, give kg and add the converted figure only when the client's units are imperial, saying so. This block stays my decision: never decide the goal, methodology, length or sessions
per week for me. When the msb_preview_program tool is available, offer to draft the block as an
import JSON (following msb://import-rules) for me to preview and approve before anything is saved,
and when it continues a program that is still an unpushed draft (a dataset=programs row whose own
status, not a block's, is "created", or a cycleId msb_create_program returned earlier), ask me
whether to add it to that program (preview with programId set to that id, never with athleteId) or
create a new program;
otherwise I will build the block in MSB myself.
