---
name: checkin-prep
description: "Briefs the coach before they reply to one client's latest check-in: a header read from 84 days back and the plan ahead, the check-in and the client's own notes quoted word for word with pain mentions marked, what changed since the check-in before it, and 2 to 4 points to address. The reply stays the coach's."
---
`<client>` below is the client the coach named when invoking this skill or in their message.
If no client is named, ask which client before calling any tool.

Brief me on <client> before I reply to their latest check-in.

Steps:
1. Call msb_list_clients with include_inactive=true and include_activity=false to get <client>'s id, status
   and units.
2. Call msb_get_client_data with dataset=overview and from set to the date 84 days before today as YYYY-MM-DD,
   leaving 'to' at its default of today: training.summary and training.keyLifts over that range,
   currentProgram, programmingLeftDays and checkins.latestQuestionnaires, the latest three questionnaires
   in it, newest first.
3. Call msb_get_client_data with dataset=checkins and from set to the date of the newest questionnaire in
   checkins.latestQuestionnaires dated before latestQuestionnaires[0]'s date (normally latestQuestionnaires[1]),
   leaving 'to' at its default of today: both questionnaires in full, the notes from that date on and
   checkins.sinceLastCheckin. When no questionnaire in the overview's list is dated before the latest one, call
   dataset=checkins with the default range, the last 28 days ending today, instead: the overview shows no
   earlier check-in to compare with, so say so and leave out the section on what changed. When the overview
   lists none, say <client> sent no questionnaire in that range.
4. Call msb_get_client_data with dataset=plan and the default range, today to 90 days ahead, for events, goals
   and checkIns.upcomingDates.
5. Write the brief, in this order:
   - Header: <client>'s name and status; the latest questionnaire's date and frequency; currentProgram's
     name and programmingLeftDays when present; training.summary.loggedSessions of prescribedSessions and
     its adherencePct over the overview's range; each key lift's first and last weekly bestE1rmKg; the next
     date in checkIns.upcomingDates; the nearest event (daysUntil) or open goal (daysLeft).
   - In their words: the latest questionnaire, checkins.questionnaires[0], or the overview's
     latestQuestionnaires[0] when the checkins result holds no questionnaire (the same row). Quote every
     free-text answer under its question as a block quote, exactly as written: no paraphrase, no spelling
     fixes, no cuts. Picks, scales and numbers follow as one compact list. Then quote, verbatim, the notes
     <client> wrote in the checkins range: workoutNotes, and the athleteNote and setComments of
     exerciseNotes. Coach notes and client notes are written on my side: leave them out.
     painMentions on an answer or a note lists up to 5 of its clauses in which the connector's English word
     list found a pain word (pain, hurt, ache, injury, strain and the like) that its rules do not read as
     negated: a word list, not a diagnosis, which also marks a word used in another sense and some reports
     of no pain ('Knee pain is gone now', 'None of my joints hurt'). Read each one in its sentence and mark
     it in bold followed by [pain] only when it does describe pain. Then mark any clause that describes pain
     the list missed (sore, tight, stiff, other words or languages, or a pain word read as negated because a
     comma is missing, as in 'Not great elbow pain again'), also quoted verbatim, and say which marks are
     yours. Question titles are never scanned and a pick or a scale never carries a mark, so a 'Yes' or
     'Left knee, a bit' to a pain question has none: read those answers too.
   - What changed since the previous check-in, headed with sinceLastCheckin.previous.date, from
     checkins.sinceLastCheckin only. Each entry of changed by its kind: number as previous → latest with
     its delta and unit, and for a scale its scale.low and scale.high labels; choice as previous → latest
     as given; other by its question alone, because one of its answers is an object (photos, measurements,
     per-day series) or the previous one was text, and neither is copied. No text answer's words appear in
     changed; the latest check-in's words are in the section above. Then addedQuestions and removedQuestions,
     unchangedAnswers in one line, and painIn, the questions whose latest answer carries painMentions: for each,
     say whether the same question's answer on the previous date (in checkins.questionnaires) carried
     painMentions too. Then between, from between.from to between.to: training.loggedSessions of
     prescribedSessions, adherencePct and lastLoggedDate; bodyWeightKg; readiness per subscale and
     sessionRpe, each avg with its n. When sinceLastCheckin.truncated is set, say a list was cut.
   - 2 to 4 points I may want to address, each tied to a quote or a number above, phrased as topics or
     questions for me, never as the reply.

adherencePct is the share of the prescribed exercises logged, null when nothing was prescribed; in between, a
session on the check-in day counts only once something that day is logged. bodyWeightKg.previous is the last
weigh-in from the start of the range up to the previous check-in, so with from set to that date only a
weigh-in on that day counts; bodyWeightKg.latest is the last weigh-in from the day after it through the latest
check-in, readings how many weigh-ins that span holds and delta the change. When bodyWeightKg.previous is
absent, give latest and readings and no change. A readiness subscale, sessionRpe or bodyWeightKg missing
from between was not logged there.
Readiness and questionnaire scales can point opposite ways: say which one a number comes from. MSB's daily
readiness runs 1 to 5 on every subscale and 5 is the best answer: sore 5 = not sore, stress 5 = not stressed,
energy 5 = very high energy, mood 5 = very happy, sleep 5 = slept very well. A questionnaire scale runs from
1 at its low label (scale.low) to 5 at its high label (scale.high), set by me per question: MSB's default
"How sore are you feeling?" runs 1 = Not sore to 5 = Very sore, the opposite of readiness sore, and its
stress question runs 1 = Very low to 5 = Very high. Describe a scale change in its labels' words; without
labels, give the numbers only and do not say which way is better.
MSB keeps no question id, so the connector pairs questions by title: a question I renamed shows as removed
plus added, and a removed question is one the latest questionnaire does not hold. When the two questionnaires
carry different frequency values, say so next to the added and removed questions. The connector does not read
which check-ins I have already answered, so this brief covers the latest one in the range either way. Check-in
dates are the client's own MSB days while ranges end on today's UTC date, so a check-in filed near midnight
can be missing from a range. The pain word list is English only.
Report measurements, not verdicts: never diagnose, name an injury or guess its cause; turn a pain mark into a
question I might ask. Do not draft my reply unless I ask for it: the reply is mine, in my words.
Loads from the connector are kilograms as MSB stores them; client.units is the client's display preference. When you quote a load you read from the connector, give kg and add the converted figure only when the client's units are imperial, saying so.
If a dataset is empty, write "no data" rather than guessing. Keep the brief under 350 words, quotes excluded.
