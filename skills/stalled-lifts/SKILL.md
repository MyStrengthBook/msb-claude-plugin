---
name: stalled-lifts
description: "Which of one client's lifts have gone flat over the last 12 ISO weeks: the weekly best e1RM per lift in a weeks-by-lift table with each flat stretch the connector marks by a stated rule, the lifts with too little or no recent data named as such, and the training, check-ins and plan phases behind each flat lift, with MSB's PR when a stretch starts at the window. Measurements, not verdicts: what to change stays the coach's call."
---
`<client>` below is the client the coach named when invoking this skill or in their message.
If no client is named, ask which client before calling any tool.

Which of <client>'s lifts are stalling over the last 12 weeks?

Steps:
1. Call msb_list_clients with include_inactive=true and include_activity=false to get <client>'s id, status
   and units.
2. Call msb_get_client_data with dataset=lift_trends and the default range, the last 12 ISO weeks ending this
   week. This first read only chooses the evidence: note which rows of liftTrends.lifts have status
   flat_stretch and which of those have stretch.atWindowStart set. Report nothing from it yet.
3. For each flat_stretch row, call msb_get_client_data with dataset=training, detail=weekly and from set to the
   date 84 days before today as YYYY-MM-DD, leaving 'to' at its default of today, with exercise set to the
   row's key when its source is lens or slot, and to the row's name for other and assistant rows and, when
   liftTrends.lens.value is streetlifting, for every lens row except the squat. A slot key matches every
   exercise in that slot whatever it is called, and any text also matches the names that contain it:
   exerciseFilter.matchedNames lists every name the read caught and keyLifts gives up to 6 of them a row each.
   When a name in the row's includes is missing from exerciseFilter.matchedNames, say the read missed it.
4. Call msb_get_client_data with dataset=checkins and the default range, the last 28 days ending today.
5. Call msb_get_client_data with dataset=plan and from set to the date 84 days before today as YYYY-MM-DD,
   leaving 'to' at its default of 90 days ahead: the phases and events during the window and after it.
6. When a flat_stretch row has stretch.atWindowStart set, call msb_get_client_data with dataset=prs once, as of
   today (the default 'to').
7. As the last call, call msb_get_client_data with dataset=lift_trends and the default range again: results are
   cached for about a minute, so this read normally costs nothing. Report from it.
   When no row is flat_stretch, skip steps 3 and 6 and still make steps 4, 5 and 7.
8. Write the answer, in this order:
   - Header: <client>'s name and status; liftTrends.lens.label and liftTrends.lens.source (the rows follow
     that lens: the client's own, the coach's or MSB's default); the weeks, from liftTrends.weeks[0] to the
     last Monday; liftTrends.counts in one line; and the rule once, with its values from liftTrends.rule: a new
     best is newBestPct % or more above the standing best, and flat_stretch means minFlatWeeks or more weeks
     without one with minLoggedSinceBest or more counted weeks since.
   - A weeks-by-lift table: one row per Monday of liftTrends.weeks, oldest first, the last one labelled
     'so far' when liftTrends.currentWeekPartial is true; one column per row of liftTrends.lifts with
     loggedWeeks of 1 or more, in their order, headed by its name; in each cell that week's value from the
     row's e1rmKg, or a dash when it is null. Put a flat_stretch row's cells from stretch.from to stretch.to
     in bold. Under the table, one legend line (weekly best e1RM in kg; a dash: no set that week, or a week
     listed in ignored; bold: the flat stretch), then the not_logged rows by name in one line.
   - Each flat_stretch row, the longest stretch.weeks first: no new best for weeksSinceBest weeks since
     stretch.bestKg in the week of stretch.from, stretch.lowKg to stretch.highKg since, stretch.endKg and
     stretch.endVsBestPct. When stretch.atWindowStart is set, write 'since at least' that week, because the
     stretch may have begun before the window, and give bestE1rm.kg and bestE1rm.date of the prs.lifts entry
     whose name equals the row's key, or say no PR row has that name. Then the evidence for the stretch's
     weeks: from training, matched by ISO week, loggedSessions of prescribedSessions, adherencePct and
     avgRpeDelta, summary.rpe, and any change between the names in keyLifts; the plan's phases and events
     that overlap the stretch or follow it; the athleteNote and setComments of the exerciseNotes for the
     names the training read matched, quoted verbatim.
   - The other rows, one line each: recent_best with bestKg and bestWeek; too_thin with loggedWeeks and its
     caveats; not_recent with latestWeek and weeksSinceLogged.
   - Every questionnaire answer, workoutNotes entry and exerciseNotes entry in the checkins result that
     carries painMentions, quoted verbatim with its date, without a diagnosis.

Quote statuses, caveats and ignored weeks as the connector computed them: never recompute a weekly best, a
stretch or a status, and never call a lift stalled or stalling. A flat_stretch row reads 'no new best for N
weeks' (its weeksSinceBest), and whether that is a stall is my call. too_thin, not_recent and not_logged say
the data is thin, stops or is missing, not that the lift is flat. The caveats: mixed_variations (the plain
lift has data in fewer than minLoggedWeeks complete weeks, so the row reads every exercise in its slot, and a
weekly best came from a variation), few_weeks (fewer than minLoggedWeeks counted weeks), sparse_since_best
(minFlatWeeks or more weeks since the best with fewer than minLoggedSinceBest counted weeks since), mixed_rpe
(mixedRpeMinWeeks or more counted weeks with an RPE and as many without), high_reps (more than half the
counted weeks above highRepsAbove reps + (10 − RPE)), as_prescribed (more than half the counted weekly bests
equal their own prescription line: MSB pre-fills actuals from the prescription, so an untouched set cannot be
told from an edited one), outlier_ignored (a week above outlierRatio × every other week) and spike_ignored (a
would-be new best spikePct % or more above both logged neighbours): each entry of ignored gives its week,
e1rmKg and reason, and that week is null in e1rmKg. The running week counts only when it sets a best or is
the row's only data (partialWeekCounted), and keeps its value in e1rmKg unless ignored lists it.
Each row of training.weeks is an ISO week (week, YYYY-Www) whose weekStart is its first day with a session,
not always the Monday: match it to liftTrends.weeks by ISO week. MSB's e1RM PR counts only the e1RM stored
on logged sets and needs 3 training days under that exact name, while the weekly series also estimates sets
with no stored e1RM, so the two can differ: say so once when you compare them.
Write the table in Markdown. Draw a chart or other visual only when I ask for one, from the same e1rmKg
values, with each flat stretch marked from stretch.from to stretch.to and a line at stretch.bestKg.
Report measurements, not verdicts: facts and, per flat lift, at most two questions I might ask the client,
each tied to a number or a quote above; never options or a plan for what to change. What to change is my
call.
Loads from the connector are kilograms as MSB stores them; client.units is the client's display preference. When you quote a load you read from the connector, give kg and add the converted figure only when the client's units are imperial, saying so.
If a dataset is empty, write "no data" rather than guessing. Keep the answer under 300 words, the table and
quotes excluded.
End the answer with the line "What to change is your call."
