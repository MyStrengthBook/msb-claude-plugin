---
name: monday-briefing
description: "Roster briefing for the week, read from the last 14 days: clients with no programming, sessions missed with none logged, programming ending within 7 days, no logged session, weigh-in or nutrition log for more than 7 days while sessions were planned, or adherence under 50%, most urgent first with the numbers behind each, then who is on track, plus three concrete actions for the coach."
---
Give me my Monday briefing for my My Strength Book roster.

Steps:
1. Call msb_list_clients with include_inactive=true and the default activity_window_days=14. An active
   client carries attention when a rule fires for them: their reasons, most urgent first, each with the
   numbers it was measured from. triage.order lists every listed client once in the order to read them:
   clients with attention by their first reason, then the other active clients, then the inactive ones.
   triage.counts cover every active client on the roster, including any the list leaves out.
2. Walk triage.order from the top and find each client in clients by id. List every client with attention
   once, under the heading of their first reason, quoting that reason's numbers; add their other reasons
   on the same line. The headings, in this order, skipping any with nobody under it:
   - No programming (noProgramming): nothing is scheduled after today; quote lastProgram when present.
   - No sessions logged (noSessions): sessions missed, none logged in the window; quote missed
     and windowDays.
   - Programming ends soon (endsSoon): quote daysLeft and the client's currentProgram when present.
   - Quiet (quiet): no logged session, weigh-in or food log for more than 7 days while sessions were planned
     in the window; quote daysSinceLastSession, bodyWeightDaysAgo and foodDaysAgo, where null means none
     logged in the window. A food log is a day with macros logged; meal photos alone do not count. A session day counts as logged once its sets are completed or marked missed, as
     on MSB's dashboard. Day counts run to today's UTC date, so near midnight one can be a day off.
   - Adherence under 50% (lowAdherence): quote adherencePct, completed and missed.
3. Then three short lines:
   - On track: triage.counts.onTrack, the active clients with workout fields read and no reason fired; it
     includes a client with nothing planned in the window (adherencePct null). Give their names,
     adherencePct and currentProgram; for a client whose adherencePct is null say "nothing planned in the
     window", never a percentage.
   - Not assessed: triage.counts.notAssessed, the active clients with no workout fields (activity null or
     the workouts view failed), with their names.
   - Inactive but signed in: flags.inactiveButLoggedInRecently, inactive clients who signed in during the
     last 14 days.
   A client who carries activityError is missing exactly the dashboard views it names
   (workouts = sessions, adherence, schedule, programming left and current programme;
   bodyWeight = body weight; nutrients = nutrition): never read a missing field as zero. A count that
   is null in triage.counts could not be measured, and quiet was checked for triage.quietEvaluated
   active clients only: say so when it matters.
4. Finish with exactly three concrete actions for me this week, each naming the client and what to do
   (message, programme, check-in), offered as options.

Keep the whole briefing under 250 words: when the names do not fit, name the clients from the top of
triage.order and give the rest as counts from triage.counts. When meta.truncated is set, say the list was
cut. Use the client names exactly as MSB lists them. Only use what the tool returned; never guess where
data is missing.
Adherence counts logged session days vs planned ones, as MSB's own dashboard does (a day whose sets were
marked missed counts as logged).
Report measurements, not verdicts: who to reach out to, and how, is my call; the three actions are options.
