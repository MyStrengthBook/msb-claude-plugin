---
name: recovery-check
description: "How one client is recovering: HRV, resting heart rate, sleep hours, the five readiness ratings and body weight over the last 7 days against the 21 days before, each with its days of data, both averages and the change, and the baseline spread when the baseline has enough days, plus the body weight slope when there are enough weigh-ins over enough days. Thin data is named as such and never read as no change. Measurements, not verdicts: what to make of them stays the coach's call."
---
`<client>` below is the client the coach named when invoking this skill or in their message.
If no client is named, ask which client before calling any tool.

How is <client> recovering? Give me a recovery check.

Steps:
1. Call msb_list_clients with include_inactive=true and include_activity=false to get <client>'s id, status
   and units.
2. Call msb_get_client_data with dataset=metrics and the default range, the last 28 days ending today, so the
   last 7 days and the 21 days before them both fall inside it.
3. Report, in this order:
   - Header: <client>'s name and status, and the two windows, trends.recentWindow and
     trends.baselineWindow, each from and to (range.from and range.to when metrics.trends is absent).
   - A table with one row per signal, in this order: HRV (hrvMs, ms), resting heart rate (restingHeartRate,
     bpm), sleep (sleepHours, hours), the readiness subscales sore, stress, energy, mood and sleep (under
     trends.readiness), then body weight (bodyWeightKg, kg). Columns: recent.days and recent.avg,
     baseline.days and baseline.avg, baseline.sd when present, change.delta, and change.pct when present.
   - Body weight slope: bodyWeightSlope.kgPerWeek kg a week over bodyWeightSlope.weighIns weigh-ins, from its
     from date to its to date. When bodyWeightSlope is absent, say the weigh-ins were too few or too close
     together for one (it needs 6 weigh-ins over at least 14 days).
   - One or two lines on the signals that moved together in the last 7 days, each with its numbers and
     without saying why, then at most two questions I might ask <client>, each tied to a number above.

Quote the trends as the connector computed them, never recomputed. change is null when either window has
fewer than 3 days: write "thin data" in that row's change columns, never that the signal held steady
or was stable, and under the table give one line from its summary in metrics.series (readiness under
series.readiness): n readings, first → last with the dates of its first and last points, min to max and avg,
plus changePct when present except on readiness, over the whole 28 days (at weekly granularity n counts weeks
and the points carry ISO week labels). A signal missing from metrics.trends has no reading in the 28 days, and
metrics.trends is absent when none has one: write "no data" in that row. meta.coverage counts the days with any
metric, steps and water included, so it can be high with none of these signals: their days are recent.days and
baseline.days. baseline.sd is given only when the baseline has 7 days or more: it is the spread of the baseline
days, so set change.delta beside it and never turn the two into a score.

MSB's daily readiness runs 1 to 5 on every subscale and 5 is the best answer: sore 5 = not sore, stress 5 = not
stressed, energy 5 = very high energy, mood 5 = very happy, sleep 5 = slept very well. MSB does not enforce
the scale, so an average above 5 is the data as stored: quote it and say it is off the scale. A readiness
change has no pct: give change.delta in points and say which way it moved in those words. Readiness sleep
is how the client rated their night and sleepHours how long they slept: keep the two apart.

MSB does not tell the connector which device measured HRV or resting heart rate, and devices measure HRV
differently, so a change of device inside the 28 days can show as a trend: say so when either one moved.
Body weight carries no unit in MSB: quote it in kg as MSB stores it, and add the converted figure only when
client.units is imperial, saying so. The trend windows end on today's UTC date, while MSB dates a reading
by the client's own day, so the newest day can be the client's yesterday or tomorrow for as many hours of
their day as the client's offset from UTC (up to 14 hours for a client far from UTC), and today may hold no
reading yet: say so rather than reading a missing last day as a gap.

Report measurements, not verdicts: no labels such as recovered, fatigued or overreached, never call a value
normal or abnormal, and no cause, diagnosis or medical advice. What the numbers mean for <client>'s
training is my call.
Write the table in Markdown. Keep the answer under 250 words, the table excluded.
