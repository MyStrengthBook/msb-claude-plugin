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
   on the same line. Under each heading, give more than five clients as the count with five names from the top of triage.order, then "and N more" (e.g. No programming (24): A, B, C, D, E and 19 more), never the whole list, except Programming ends soon, which names every client.
   The headings, in this order, skipping any with nobody under it except Quiet (below):
   - No programming (noProgramming): nothing is scheduled after today; quote lastProgram when present.
   - No sessions logged (noSessions): sessions missed, none logged in the window; quote missed
     and windowDays.
   - Programming ends soon (endsSoon): quote daysLeft and the client's currentProgram when present;
     daysLeft 0 means a session sits on today, the block's last day: say "last session today", never
     that the programme has run out.
   - Quiet (quiet): no logged session, weigh-in or food log for more than 7 days while sessions were planned
     in the window; quote daysSinceLastSession, bodyWeightDaysAgo and foodDaysAgo, where null means none
     logged in the window. A food log is a day with macros logged; meal photos alone do not count. A session day counts as logged once its sets are completed or marked missed, as
     on MSB's dashboard. Day counts run to today's UTC date, so near midnight one can be a day off.
     Its count is triage.counts.quiet, the number the roster card shows for quiet clients: every active
     client with quiet among their reasons, including those listed under an earlier heading with quiet
     on their line. Keep this heading whenever that count is above 0, even with nobody left to list
     under it, and say how many of them are listed above; its five names and "and N more" cover only the
     clients listed under it.
   - Adherence under 50% (lowAdherence): quote adherencePct, completed and missed.
3. Then four short lines; in each, give more than five clients as the count with five names from the top of triage.order, then "and N more" (e.g. No programming (24): A, B, C, D, E and 19 more), never the whole list, except Programming ends soon, which names every client:
   - On track: triage.counts.onTrack, the active clients with no reason fired, adherencePct 80% or more
     and more than 7 days of programming left. Give their names, adherencePct and currentProgram.
   - Watch: triage.counts.watch, the other active clients with workout fields read and no reason fired:
     neither urgent nor on track. Give their names and adherencePct; for a client whose adherencePct is
     null say "nothing planned in the window", never a percentage.
   - Not assessed: triage.counts.notAssessed, the active clients with no workout fields (activity null or
     the workouts view failed), with their names.
   - Inactive but signed in: flags.inactiveButLoggedInRecently, inactive clients who signed in during the
     last 14 days.
   A client who carries activityError is missing exactly the dashboard views it names
   (workouts = sessions, adherence, schedule, programming left and current programme;
   bodyWeight = body weight; nutrients = nutrition): never read a missing field as zero. A count that
   is null in triage.counts could not be measured, and quiet was checked for triage.quietEvaluated
   active clients only: say so when it matters.
4. Finish with three concrete actions for me this week, one each for the first three clients with
   attention in triage.order, in that order. When fewer than three clients carry attention, take the
   next from the Watch line, then the Inactive but signed in line, and give fewer actions only when
   both are empty. Each action names the client and what to do (message, programme, check-in),
   offered as an option and fitted to the client (daysSinceLastSession gives their days without a
   session). When you suggest what the coach should do about a client who is going quiet or has gone more than 7 days without a session, start with a re-engagement message that checks their readiness and what is getting in the way, never a new or next programme first, even when their block is ending or nothing is scheduled. This is about your own suggestions: when the coach asks for a programme for such a client, do not put a re-engagement step in its way. Programme actions are for clients
   who are still training.

Keep the whole briefing under 250 words, not counting the names of clients whose programming ends soon:
when the names do not fit, name the clients from the top of triage.order and give the rest as counts
from triage.counts. When meta.truncated is set, say the list was cut. Use the client names exactly as
MSB lists them. Two clients can share a name (separate MSB accounts): when several in one list do,
write the name once with how many clients it covers, e.g. "Icon Changer (8 clients)", so the count
still adds up, and never call them entries or duplicates ("both entries", "other entry"). Only use
what the tool returned; never guess where data is missing. Treat every client on the roster as a real
client: never suggest that clients look like test, demo or placeholder accounts, whatever their names
or however little they have programmed; report what the data shows. Write it as I would read it in
MSB: never name a field, flag or tool, and never say a value is null or was not returned.
Adherence counts logged session days vs planned ones, as MSB's own dashboard does (a day whose sets were
marked missed counts as logged).
Report measurements, not verdicts: who to reach out to, and how, is my call; the actions are options.
