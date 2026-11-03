---
name: nutrition-check
description: "How one client is eating: a table of the last 7 logged days (calories counted from the logged protein, carbs and fat, the training or rest day target and day adherence), then days logged, averages, days on target and adherence to plan, as measurements for the coach to weigh."
---
`<client>` below is the client the coach named when invoking this skill or in their message.
If no client is named, ask which client before calling any tool.

How is <client> eating? Give me a nutrition check.

Steps:
1. Call msb_list_clients with include_inactive=true and include_activity=false to get <client>'s id and
   status.
2. Call msb_get_client_data with dataset=nutrition and the default range, the last 28 days ending today, so
   the table and the summary describe the same days.
3. Report, in this order:
   - a table of the last 7 logged days in date order, one row per day: date, day type (dayType, training or
     rest), calories, target calories (targetCalories), day adherence % (adherencePct), then protein, carbs
     and fat in grams. A logged day is a row of nutrition.days that has a dayType; when fewer than 7 days
     were logged, show the ones there are and say how many;
   - logging: summary.daysLogged of summary.daysInRange days (summary.loggingPct %) and the last logged
     date, summary.lastLoggedDate;
   - intake: summary.avgCalories, avgProteins, avgCarbs and avgFats next to each plan in nutrition.plans
     (its from and to dates, calories and macros, and its restDay targets when it has them). The averages
     mix training and rest days; the targets line compares each day with its own target;
   - targets: summary.targets.daysOnTarget of summary.targets.daysWithTarget scored days on target,
     summary.targets.dayAdherencePct, and summary.adherenceToPlanPct (calories eaten over target calories
     on those same days).

Calories are calculated by the connector from the logged macros (4 kcal per gram of protein and carbs, 9 per
gram of fat) because MSB stores no calorie total: say so once. The calories come from the unrounded grams, so
they can differ by a few kcal from 4/4/9 of the rounded grams in the row: never correct them. When a plan's
calories is 0, its calorie target is 4 kcal per gram of protein and carbs plus 9 per gram of fat, as
summary.planCalories and each day's targetCalories already show. adherencePct is MSB's day adherence: 100
minus the average miss on protein, carbs and calories, each as a % of its target (never below 0), so fat does
not count, and a day is on target when that miss is under 10%. A day without targetCalories has no plan
target (no plan covers it, or its plan sets no calories or macros): write "no plan" and never invent a
target. On a logged day, a protein, carbs or fat value missing from the row counts as 0 g, as it does in the
averages. A day with targetCalories but no adherencePct has a target MSB cannot score: write "not scored".
When summary.targets is absent, no nutrition plan covers the range: say so plainly. When
summary.daysLogged is 0, say plainly that no macros were logged in the last 28 days, name any dates that
have meals only, and stop. When days were logged but summary.targets.daysWithTarget is 0, no logged day
could be scored: say so, and leave day adherence and adherence to plan out rather than writing 0 of 0.
A date missing from nutrition.days was not logged, and a row with mealsLogged but no dayType had meals logged
without macros: name those dates and never read either as zero intake. Dates are MSB's calendar days and the
range ends on today's UTC date, so today's row may still be filling up and, near midnight, the newest day can
be the client's yesterday or tomorrow: say so rather than calling a short last day low; the summary
averages and adherence include that day too, so say they include a day still in progress.
Report measurements, not verdicts: no judgement words about what the client ate, no diagnosis and no new
targets. This connector cannot set targets or change a nutrition plan; what to do next is my call.
