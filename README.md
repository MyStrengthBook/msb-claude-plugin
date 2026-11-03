# MyStrengthBook plugin for Claude and ChatGPT

Bundles the MyStrengthBook Agent connector with the skills below. Coaches read their clients' coaching data and save, extend or delete program drafts.
This pack has 12 skills. In Claude, type `/` and pick one; in ChatGPT, type `@`.

- `monday-briefing`: Roster briefing for the week, read from the last 14 days: clients with no programming, sessions missed with none logged, programming ending within 7 days, no logged session, weigh-in or nutrition log for more than 7 days while sessions were planned, or adherence under 50%, most urgent first with the numbers behind each, then who is on track, plus three concrete actions for the coach.
- `client-review`: Deep review of one client over the last N months (up to 12): coverage, adherence by block and by week, RPE, key lifts and PRs, current programme, body weight, nutrition, check-ins, and what changed when.
- `re-engagement-plan`: For a client who went quiet: a short warm message from the coach plus a two-week reintroduction plan built from their last logged loads at deload intensity.
- `coach-dashboard`: One-page dashboard for a client: headline numbers, current programme, adherence by week, key lifts and PR tiles, flags and next steps.
- `next-block-suggestion`: Reads a year of training by programme block plus programmes, PRs and the plan, summarises what each block did to the key lifts and RPE, and proposes the structure of the next block for the coach to decide on.
- `nutrition-check`: How one client is eating: a table of the last 7 logged days (calories counted from the logged protein, carbs and fat, the training or rest day target and day adherence), then days logged, averages, days on target and adherence to plan, as measurements for the coach to weigh.
- `checkin-prep`: Briefs the coach before they reply to one client's latest check-in: a header read from 84 days back and the plan ahead, the check-in and the client's own notes quoted word for word with pain mentions marked, what changed since the check-in before it, and 2 to 4 points to address. The reply stays the coach's.
- `stalled-lifts`: Which of one client's lifts have gone flat over the last 12 ISO weeks: the weekly best e1RM per lift in a weeks-by-lift table with each flat stretch the connector marks by a stated rule, the lifts with too little or no recent data named as such, and the training, check-ins and plan phases behind each flat lift, with MSB's PR when a stretch starts at the window. Measurements, not verdicts: what to change stays the coach's call.
- `programming-check`: Did the last block work? For one client's programme block (the last one unless the coach names another by programme name or a date), read from 6 months of blocks: prescribed against logged RPE, load and reps, the e1RM change per lift across the block and the accessory sets skipped, with set quotes from the block's last 14 days and the client's check-ins, then a 3 to 5 sentence block verdict to weigh that names the facts for and against it. The verdict is a starting point: the coach's judgment decides.
- `recovery-check`: How one client is recovering: HRV, resting heart rate, sleep hours, the five readiness ratings and body weight over the last 7 days against the 21 days before, each with its days of data, both averages and the change, and the baseline spread when the baseline has enough days, plus the body weight slope when there are enough weigh-ins over enough days. Thin data is named as such and never read as no change. Measurements, not verdicts: what to make of them stays the coach's call.
- `build-program`: Builds the next training program for one client from their own MSB data (training, check-ins in full text, PRs, plan and the lens when present): reads the msb://import-rules resource and the coach's library, previews the import JSON with msb_preview_program, shows the coach the summary and every new library movement, and saves it with msb_create_program as an invisible draft only on the coach's go.
- `convert-program`: Converts a training program the coach shares (xlsx, csv, PDF, docs or pasted text) into MSB's import JSON using the msb://import-rules resource and the coach's own exercise library, previews it with msb_preview_program, and saves it with msb_create_program as an invisible draft (client or template) only on the coach's go.

The connector is reached at `https://mcp.mystrengthbook.com/mcp`. On first use the app opens the MSB login; sign in with
your MSB account and allow access. Coaching reads are read-only, programs are saved, and blocks added to them, only as drafts you review first, and a program is deleted only after you confirm a preview and only if it was never pushed itself.

## Install

- claude.ai / Claude Desktop: Customize > Plugins > Add > Upload plugin, and pick the zip.
- Claude Code: `claude --plugin-dir <path-to-this-folder>`.
- ChatGPT desktop app: Plugins > Add > Upload plugin archive, and pick the zip. ChatGPT runs a
  plugin's connector in its desktop app only; on chatgpt.com, add the connector by URL as https://mcp.mystrengthbook.com/ shows.

Upgrading from the old `msb-coach` plugin: remove it first (claude.ai or Desktop > Customize > Plugins, or `claude plugin uninstall msb-coach`), otherwise the skills appear twice.
