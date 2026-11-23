---
name: build-meal-plan
description: "Builds a meal plan for the coach, or converts one they share, into MSB's meal plan JSON from their own foods, meals and recipes: asks for the plan mode, calories, macros, days and weeks, reads the msb://meal-plan-rules resource and the coach's meal library, previews it with msb_preview_meal_plan with each day's totals against the targets, and saves it with msb_create_meal_plan as a meal plan template only on the coach's go."
---
`<plan_mode>` below is what the coach gave for it when invoking this skill or in their message, otherwise "not given: ask the coach".
`<calories>` below is what the coach gave for it when invoking this skill or in their message, otherwise "not given: ask the coach".
`<macros>` below is what the coach gave for it when invoking this skill or in their message, otherwise "not given: ask the coach".
`<days>` below is what the coach gave for it when invoking this skill or in their message, otherwise "not given: ask the coach (a detailed plan only)".
`<weeks>` below is what the coach gave for it when invoking this skill or in their message, otherwise "not given: ask the coach (a detailed plan only)".
`<plan_file>` below is what the coach gave for it when invoking this skill or in their message, otherwise "none: build the plan from the targets".

Build a meal plan for me and save it as a meal plan template in MSB.

Plan mode: <plan_mode>
Daily calories: <calories>
Macros: <macros>
Days per week: <days>
Weeks: <weeks>
Source to convert: <plan_file>

If msb_preview_meal_plan is not among your tools, this MSB connection can only read: tell me to
reconnect MSB and allow program drafts, and stop.

Steps:
1. Read the meal plan rules FIRST: call msb_get_my_profile with include_meal_plan_rules=true (the same
   text as the msb://meal-plan-rules resource). Self-check every JSON you write against them.
2. If the plan mode, calories, macros, days or weeks were not given above (or by the source), ASK ME
   for all of them in one message before building anything. A simplified template is one day of
   eating; for several days, ask whether I want one simplified template per day or a detailed plan.
   These are my targets: never pick them yourself.
3. Call msb_get_my_profile with include_meal_library=true. Use my own foods, saved meals and recipes,
   by their exact names, wherever they fit; a recipe's quantity is servings, and its perServing gives
   the macros of one serving. Add a new food (with estimate) or a new meal only when mine have nothing
   that fits.
4. When there is a source, read it completely and convert it per the rules; otherwise build the
   plan from my targets with everyday, varied meals.
5. Add up each day: calories, protein, carbs and fats. Adjust the quantities until every day is
   within about 5% of my targets; name any day that cannot be.
6. Call msb_preview_meal_plan with the plan (and source set to the file name when you converted a
   file). A preview writes nothing. If errors come back, read every one, fix the JSON and preview
   again. Never hide or shorten an error.
7. Show me the preview in full: the name and mode, the weeks and days, a table of each day's
   calories, protein, carbs and fats against my targets, every new food and new meal, every match
   worth checking, and what was not imported and what you assumed. When I pick one of a new food's
   suggestions instead, preview again with choices set to it. Then ask for my go.
8. Only when I say go: call msb_create_meal_plan with only the preview_id from the preview I
   approved. Report the link, and remind me the template is not assigned to any client until I do
   it in MSB.
