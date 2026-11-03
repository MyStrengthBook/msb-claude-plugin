---
name: build-program
description: "Builds the next training program for one client from their own MSB data (training, check-ins in full text, PRs, plan and the lens when present): reads the msb://import-rules resource and the coach's library, previews the import JSON with msb_preview_program, shows the coach the summary and every new library movement, and saves it with msb_create_program as an invisible draft only on the coach's go."
---
`<client>` below is the client the coach named when invoking this skill or in their message; `<goal>` is any further text they gave after the client name, otherwise "not given: ask the coach for the goal before building anything"; `<methodology>` is any further text they gave after the client name, otherwise "not given: ask the coach which methodology to follow; their own approach wins over any default"; `<weeks>` is any further text they gave after the client name, otherwise "not given: ask the coach how many weeks this program should run"; `<days_per_week>` is any further text they gave after the client name, otherwise "not given: ask the coach how many sessions per week"; `<constraints>` is any further text they gave after the client name, otherwise "none given: ask the coach about injuries, equipment and time limits before building".
If no client is named, ask which client before calling any tool.

Build the next training program for my client <client> and save it as a draft in MSB.

Goal: <goal>
Methodology: <methodology>
Length: <weeks>
Sessions per week: <days_per_week>
Constraints: <constraints>

If msb_preview_program is not among your tools, this MSB connection can only read: tell me to
reconnect MSB and allow program drafts, and stop.

Steps:
1. Read the msb://import-rules resource FIRST. It is the exact format msb_preview_program accepts;
   self-check every JSON you write against it before calling the tool.
2. Call msb_list_clients with include_inactive=true to get <client>'s id, status and units.
3. Read the evidence: msb_get_client_data with dataset=training, detail=blocks and from set to the date
   12 weeks before today as YYYY-MM-DD, then detail=weekly for the last 4 weeks; dataset=checkins for
   the last 8 weeks (the questionnaire answers in the client's own words: quote them, don't
   paraphrase); dataset=prs (oneRepMaxes are the stored 1RMs MSB takes a % of; the PR history and e1RMs
   are evidence of current strength only); dataset=plan with 'to' set to the date 180 days from today as
   YYYY-MM-DD (upcoming events and open goals); dataset=overview (judge the programming through the
   client's training lens, overview.lens, when it is present); and dataset=programs (a row whose own
   status, not a block's, is "created" is a program that is still an unpushed draft).
4. If the goal, methodology, length, sessions per week or constraints were not given above, ASK ME
   before writing any program, offering two or three options grounded in the data. If this block
   continues a program that is still an unpushed draft (from dataset=programs, or a cycleId
   msb_create_program returned earlier), ask me in the same message whether to add it to that program
   (preview with programId set to that id, never with athleteId) or create a new program: added
   blocks count toward the 20-week total together with that program's existing weeks, and the
   program keeps its own name and description. This is my client and my programming:
   never decide these yourself, and never move on before I have answered.
5. Call msb_get_my_profile with include_exercise_library=true to get my exerciseLibrary and
   primaryExercises in the import JSON's shape. Every movement you use must appear in the program's
   exerciseLibrary under the same primary, matched to my library names exactly; write a new entry only
   when my library has nothing that fits (at most 30 new movements).
6. Write the import JSON per msb://import-rules: blocks whose weeks may differ in number of workouts
   (keep test and deload weeks in their block, number each week's days 1, 2, 3… with no gaps, never
   add placeholder workouts), program.loadUnit set to the unit the loads are given in ("lb" for
   pounds; never convert loads yourself), competition lifts as primary-only, variations and assistant
   movements with exact library names, supersets with supersetWithNext, reps as whole numbers or range
   strings ("8-10") or "AMRAP", RPE 5-10. Prefer a percentRepMax on competition lifts, as a % of the
   client's stored 1RM from prs.oneRepMaxes (a listed movement's own kg, else its lift's; the kg next to
   a % is that max × the %), never of an e1RM; fixed loads only when I gave them. A lift missing from
   liftsKg or a movement with kg null has no stored max: a % on it comes out 0 on the draft and a fixed
   load on it goes into the exercise notes, so tell me to set that 1RM in MSB before I create.
7. Call msb_preview_program with program set to the import JSON and, when I chose to add the block
   to that program, programId set to its id (never with athleteId; if I have not answered that yet,
   ask me first), otherwise athleteId set to <client>'s id. A preview writes nothing. If errors
   come back, read every one, fix the JSON and preview again. Never hide or shorten an error.
8. Show me the preview in full: the program name and description (when the result carries
   appendedTo, the program the blocks go to instead: appendedTo.name, whether it is my template or
   <client>'s draft, and its existing block count, since it keeps its own name and description),
   each block with its weeks and sessions per week, exercise counts, supersets,
   EVERY new library movement (libraryAdditions) and every library fill. Name every lift in
   clientMaxesMissing: <client> has no 1RM for it, so its fixed loads went into the exercise notes;
   tell me to set that 1RM in MSB before I create to keep those loads, otherwise I must enter them by
   hand. Then ask for my go.
9. Only when I say go: call msb_create_program with only the preview_id from that clean preview,
   so the exact previewed program is created (or its blocks added to that program) and nothing else is
   sent. If I ask for changes instead, fix the JSON and preview again; only the new preview_id may be
   created. Report the cycleId and the link, and remind me that <client> cannot see the draft until
   I open it in MSB, review it and push it to their calendar myself.

Loads from the connector are kilograms as MSB stores them; client.units is the client's display preference. When you quote a load you read from the connector, give kg and add the converted figure only when the client's units are imperial, saying so. Nothing is ever sent to the client: the program lands as an invisible draft I control.
