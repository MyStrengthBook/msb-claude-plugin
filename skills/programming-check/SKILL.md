---
name: programming-check
description: "Did the last block work? For one client's programme block (the last one unless the coach names another by programme name or a date), read from 6 months of blocks: prescribed against logged RPE, load and reps, the e1RM change per lift across the block and the accessory sets skipped, with set quotes from the block's last 14 days and the client's check-ins, then a 3 to 5 sentence block verdict to weigh that names the facts for and against it. The verdict is a starting point: the coach's judgment decides."
---
`<client>` below is the client the coach named when invoking this skill or in their message; `<block>` is any further text they gave after the client name, otherwise "the last block".
If no client is named, ask which client before calling any tool.

Were my RPEs right for <client>? Check this block: <block>.

Steps:
1. Call msb_list_clients with include_inactive=true and include_activity=false to get <client>'s id, status
   and units.
2. Call msb_get_client_data with dataset=training, detail=blocks and from set to the date 182 days before today
   as YYYY-MM-DD, leaving 'to' at its default of today and exercise unset: a filter would scope every block
   figure to the names it matched. Pick one row of training.blocks as the block:
   - The last block is the row with a cycleId and loggedSessions of 1 or more that has the latest lastDate (the
     later firstDate on a tie). When its programProgress is "In Progress", say the block is still running, so
     the check covers it so far, and in one line name the block before it by the same rule (programName, name,
     firstDate to lastDate), so I can ask for that one.
   - A block I named instead is matched by programme name, the latest row whose programName or name contains
     the text, ignoring case, or by a date, the row whose firstDate to lastDate holds it. When nothing matches,
     list the rows (programName, name, firstDate to lastDate) and ask me which one before any other call.
   The row with a null cycleId holds the training outside any programme and is never the block: when no other
   row has loggedSessions of 1 or more, say no programme block was found in the last 6 months and stop.
   When the picked row has no check, or its key lifts carry no check (a meta.notes line says the size budget
   left checks out, or the block is not one of the 2 latest programme blocks, the only ones a read gives lift
   checks), call msb_get_client_data again with dataset=training, detail=blocks, from set to the block's
   firstDate and 'to' set to its lastDate, exercise unset, and use that read's row for the block (the same
   cycleId, program and programInstance).
3. Call msb_get_client_data with dataset=training, detail=sets, 'to' set to the block's lastDate and from set to
   the later of its firstDate and the date 13 days before its lastDate (YYYY-MM-DD), exercise unset. When the
   result's granularity is not sets, the size budget dropped the per-set rows: call it again with exercise set
   to the name of the block's first key lift, and say a name also catches that lift's variations
   (exerciseFilter.matchedNames lists every name the read caught); when the block has no key lift, call it
   again with from set to the later of its firstDate and the date 6 days before its lastDate instead. When
   that read's granularity is still not sets, say the set rows did not fit and quote no set.
4. Call msb_get_client_data with dataset=checkins, from set to the block's firstDate and 'to' set to the earlier
   of today and the date 7 days after its lastDate.
5. Write the answer, in this order:
   - Header: <client>'s name and status; the block's programName, name, programInstance, firstDate to lastDate
     and programProgress, and the rule that picked it; loggedSessions of prescribedSessions and adherencePct (to
     today's UTC date, so today's session is in them), and check.sessions, the trained and untrained sessions.
   - Prescribed vs logged RPE, load and reps, from the block's check: check.rpe (setsCompared, avgPrescribed →
     avgActual, avgDelta, overshootPct and undershootPct (1 RPE or more off), and onTargetPct, the share of sets
     with logged RPE equal to prescribed, a 0.5 miss being in none of the three); check.load (setsCompared,
     avgDeltaKg and avgDeltaPct, overPct, underPct and withinPct, within 1.25 kg of the target), with
     check.load.setsOnDefault1rm kept apart: those sets are in no load figure because their max may be MSB's
     default 1RM; check.reps (setsCompared, avgDelta, belowPct, onTargetPct, abovePct, and amrapSets, the AMRAP
     sets with no rep target); check.unchangedPct. Then one or two telling sets from step 3 dated inside the
     block, each with its date and exercise: the line's target (prescribed, or its prescribedLines entry when
     the set has a line) against the logged reps, loadKg and rpe, its loadDeltaKg, repsDelta and unchanged, and
     the set's comment verbatim when it has one.
   - e1RM change per lift across the block, one line per row of the block's keyLifts: sessions, firstE1rmKg →
     bestE1rmKg → lastE1rmKg and changePct, then the lift's own check when it has one (its rpe, load and
     reps, read as above).
   - Accessory sets skipped: check.sets.accessory planned, completed, skipped and markedMissed, then
     check.sets.primary and check.sets.variation the same way, one line each.
   - In their words: up to three quotes about training from step 4 (questionnaire answers, workoutNotes, the
     athleteNote and setComments of exerciseNotes), verbatim with their dates.
   - Block verdict to weigh: 3 to 5 sentences on whether the block was pitched right, naming the facts above
     that support that reading and the facts that cut against it, each with its number.

Quote the check, keyLifts and set fields as the connector computed them, never recomputed;
meta.prescriptionRules states the rule behind each one. A part of a check that is null, missing or has
setsCompared 0 had nothing to compare: say so, never write 0. Lift checks cover only the 2 programme blocks
with a key lift and the latest lastDate in a read, so when the block's key lifts still carry no check after
the re-read in step 2, say so and use the block's check.
When a meta.notes line says the size budget left checks out, say they were left out, never that they are
zero. A read with meta.degraded set can also have kept fewer key lifts per block or dropped the per-set rows,
which no note names: report what it returned and say it was shortened to fit.
MSB pre-fills actuals from the prescription, so an untouched set cannot be told from an edited one: an
unchanged set, and a logged RPE equal to prescribed, may never have been edited, so read unchangedPct and
onTargetPct as that, never as proof the prescription fit. Sets on a %1RM line whose max may be MSB's default
1RM (repMaxIsDefault: 100 kg, 45.359 kg or never set) carry no loadDeltaKg: say how many the load comparison
left out. MSB has no skipped status: skipped is planned minus completed on trained sessions, the sets not
ticked as completed, so a skipped set may have been done but not logged, and markedMissed is the part the
client marked missed. Check-in dates are the client's own MSB days while ranges end on today's UTC date, so
a check-in filed near midnight can be missing from a range; sessions and skips count only days before the
client's local today (before UTC yesterday when MSB has no time zone for them), so today's session is never
counted. Say where the data is too thin to conclude: fewer than 3 trained sessions in the block, or fewer
than 3 sessions of a lift.
Every section before the verdict reports measurements as the connector computed them. The verdict is a
starting point for me, never a fact, and comes with no plan, options or changes for the next block: my
judgment decides.
Loads from the connector are kilograms as MSB stores them; client.units is the client's display preference. When you quote a load you read from the connector, give kg and add the converted figure only when the client's units are imperial, saying so.
If a dataset is empty, write "no data" rather than guessing. Keep the answer under 350 words, tables and
quotes excluded.
End the answer with the line "Your call: the verdict is a starting point, your judgment decides."
