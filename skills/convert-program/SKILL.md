---
name: convert-program
description: "Converts a training program the coach shares (xlsx, csv, PDF, docs or pasted text) into MSB's import JSON using the msb://import-rules resource and the coach's own exercise library, previews it with msb_preview_program, and saves it with msb_create_program as an invisible draft (client or template) only on the coach's go."
---
`<file>` below is the program the coach shared when invoking this skill or in their message: an attached xlsx, csv, PDF or doc, or program text they pasted. If they shared nothing, ask for the program before calling any tool.
`<client>` is the client they named for it, exactly as msb_list_clients shows them, otherwise "no client: save it as a coach template".

Convert the training program in <file> into MSB's import JSON and save it as a draft in MSB.
For: <client>

If msb_preview_program is not among your tools, this MSB connection can only read: tell me to
reconnect MSB and allow program drafts, and stop.

Steps:
1. Read the msb://import-rules resource FIRST: the format (structure, exercise and sets rules), the
   workout-notes and athlete's-choice conventions and the Excel date trap. Self-check every JSON you
   write against it before calling the tool.
2. Read <file> completely, whatever its form: xlsx, csv, PDF, docs or pasted text. In a
   spreadsheet, read every tab and every side-by-side column group. Tabs named Week 1, Week 2… are
   usually weeks of ONE block; tabs that are versions or variants of a full program are separate
   blocks. Weeks of a block may have different numbers of workouts (keep test and deload weeks in
   their block); each workout needs at least one exercise. Skip overview, legend and instructions
   tabs or pages, and move useful overview text into the program description.
3. Call msb_get_my_profile with include_exercise_library=true to get my exerciseLibrary and
   primaryExercises in the import JSON's shape: this is the library the rules assume is attached.
   Match movement names to mine exactly (strip muscle-group suffixes, fix obvious typos), and include
   the full library entry for every movement used, plus a new entry for movements mine lacks. Note
   every movement you renamed to match my library: the name as written and my library name.
   When the program is for a named client, also get their id from msb_list_clients and call
   msb_get_client_data with dataset=programs for them (a row whose own status, not a block's, is
   "created" is a program that is still an unpushed draft). If the source continues such a program,
   or a cycleId msb_create_program returned earlier, ask me before writing the JSON whether to add its
   blocks to that program (preview with programId set to that id, never with athleteId) or create a
   new program: added blocks count toward the 20-week total together with that program's existing
   weeks, and the program keeps its own name and description.
4. Write the import JSON per the rules: one file or one pasted text = one program; the program name
   the source gives, otherwise the file name (ask me when pasted text names none); week starting at
   1 within each block, day = the session number within the week; instructions inside a name go to
   notes; workout-level notes go in the session's notes, never an exercise;
   never drop coaching information silently: anything that does not fit goes into the closest notes
   field. Set program.loadUnit to the unit the loads are given in ("lb" for pounds) and
   never convert loads yourself.
5. Call msb_preview_program with program set to the import JSON and:
   - source set to the file name (or "pasted text" when I pasted it);
   - spellingFixes set to one {from, to} pair per movement you renamed in step 3 (from = the name as
     written, to = my library name); leave it out when you renamed nothing;
   - programId set to that program's id when I chose to add the blocks to it (never with athleteId;
     if I have not answered that yet, ask me first);
   - otherwise athleteId set to the named client's id from msb_list_clients when the program is for a
     specific client; leave it out for a coach template.
   A preview writes nothing. If errors come back, read every one, fix the JSON and preview again.
   Never hide or shorten an error.
6. Show me the preview in full: the program name and description (when the result carries
   appendedTo, the program the blocks go to instead: appendedTo.name, whether it is my template or a
   client's draft, and its existing block count, since it keeps its own name and description), each
   block with its weeks and sessions per week, exercise counts, supersets, EVERY new library movement
   (libraryAdditions), every library fill and every spelling you fixed. For a client draft, name every
   lift in clientMaxesMissing: the client has no 1RM for it, so its fixed loads went into the exercise
   notes; tell me to set that 1RM in MSB before I create to keep those loads, otherwise I must enter
   them by hand. Then ask for my go.
7. Only when I say go: call msb_create_program with only the preview_id from that clean preview,
   so the exact previewed program is created (or its blocks added to that program) and nothing else is
   sent. If I ask for changes instead, fix the JSON and preview again; only the new preview_id may be
   created. Report the cycleId and the link, and remind me the draft is invisible to the client until
   I open it in MSB, review it and push it to their calendar myself.
