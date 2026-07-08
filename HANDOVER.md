# HANDOVER — Global Riders/Teams Database + Guildford Tool 2026

**Built:** 6 July 2026 (Claude Fable session). This file is the brief for follow-up data-entry/verification sessions (use a lower-usage model, e.g. Sonnet). **Do not restructure code or schemas — only add/verify data.**

## Architecture

```
Bike Races/
├── Riders:Teams Database/          ← GLOBAL database (all events, futureproof)
│   ├── riders.json  + riders.js    ← 528 riders (js = window.GLOBAL_RIDERS, auto-written by admin)
│   ├── teams.json   + teams.js     ← 159 teams  (js = window.GLOBAL_TEAMS)
│   ├── results-archive.json/.js    ← 74 full race result tables (window.RESULTS_ARCHIVE)
│   ├── admin.html                  ← rider/team editor; paste images; writes .json + .js in place (Chrome/Edge)
│   ├── images/riders/  images/kits/ ← photos & kit images (paste in admin saves here automatically)
│   └── HANDOVER.md                 ← this file
└── Guildford Tool/
    ├── index.html                  ← commentary tool; loads ../Riders:Teams Database/*.js + race-data.js
    ├── race-data.js                ← event data: startlists, timetable, series, Rapha, sponsors, history
    └── data/race-data.json         ← reference copy of the same data
```

- The tool reads the DB **live via `<script src>`** — edit in admin, save, refresh tool. No rebuild step.
- Startlist edits (late entries on the line) are made in the tool's **Rider Startlist** tab, persist in localStorage, and can be exported as a fresh `race-data.js` (button in that tab). To make edits permanent, replace `race-data.js` with the export.
- For a future event: copy the `Guildford Tool` folder, replace `race-data.js`. The database folder is shared.

## Schemas (do not change keys)

**Rider:** id, name, team, team_history[{team,years}], category (Elite Open/Elite Women/Youth/Local Heroes), gender (M/F, youth only), dob, age, bc_age_category (Youth A–E/Junior/…), bc_ability_category (Elite/1st/2nd/3rd/4th), nationality, home_region, local_connection, titles{olympic[],world[],european[],national[]}, image_rider, results[{race,result,year,rider_specific_race_info}], needs_review, notes.

**Team:** id, name, team_status, location, previous_names[{name,years}], directeurs_sportifs[{name,ex_pro,bio}], coaches[…], social_media[{handle,platform}], notable_results[], results_2026[], club_activities, targets_2026, other, kit_image, needs_review.

**Archive entry:** {event, year, race, rows[{pos,name,club,status,cat,pts | time}]} — youth tables, hover popups and history dropdowns read this by name-match at runtime.

## What was imported this session

- 232 riders + 90 teams migrated from the Otley tool (newest data, from its index.html).
- All six Guildford 2026 startlists (Open 70+8res, Women 69, U8 26, U10/12 76, U14/16 71, Local Heroes 70). Elite lists transcribed from scanned PDFs; open bib 67 Ben Elvin marked withdrawn (struck through on source), bib 70 was blank on the source.
- 296 new riders auto-created from startlists (flagged `needs_review`), 69 new teams/clubs.
- Guildford youth + Local Heroes + elite results 2021, 2022, 2024, 2025 (full tables → archive; placings attached to riders). 2023 elite from The British Continental report.
- 2026: Colne/Otley/Ilkley results with time gaps, NCS standings after R1 & R3 (individual/team/sprint), National Circuit Champs, Lincoln GP, Rutland–Melton, Women's CiCLE, London Nocturne.
- Rapha Super League: official 1 Jul standings + **computed** provisional "after Otley" (from published points allocation).
- Sponsors from the race manual + email + web research.

## Verification / data-entry checklist (in priority order)

1. **needs_review riders (296)** — filter in admin. Youth/LH riders mostly just need club confirmation; add DOB/region where known. Elite additions (e.g. Foran CT, DRAFT, Stolen Goat riders new to the DB) deserve proper profiles.
2. **Youth club names** — PDF text wrapping was re-joined by heuristic (e.g. "Southborough & District Whls"). Spot-check odd-looking club names in admin against the startlist PDFs in Guildford Documents.
3. **2023 gap** — no youth/Local Heroes results for 2023 in the source folder (only the elite BC report). If Henry finds the 2023 confirmed-results sheet, parse it into the archive like the other years (see scripts note below).
4. **2025 U8 races** — absent from the 2025 confirmed results PDF (results start at U10). Confirm whether U8 ran in 2025.
5. **2023 BC elite results** — rider/team split used a two-word-name heuristic; check names like "Huw Buck Jones" in the archive (year 2023).
6. **Rapha provisional standings** — computed, marked "(calc)" in the tool. Replace with the next official publication when it appears (edit `race-data.js` → rapha.official, and set provisional = official copy).
7. **Bell & Spoke sponsor blurb** — placeholder; confirm what the business is.
8. **Women's team/sprint standings after R3** — not in the published PDF (R1 shown as fallback). Add if BC publishes them.
9. **Duplicate-name risk** — youth results were attached by exact name match. If a rider looks like they have someone else's result, fix in admin (results table per rider).
10. **Images** — 23 rider photos + 71 kits carried over. Paste new ones in admin (rider page / team page → paste zone). Filenames are auto-slugged.

## Notes for scripts

The parsing scripts from this session lived in /tmp (sandbox) and are gone; the method that worked: `pdfplumber.extract_words()`, bucket words into columns by x0 ranges (Guildford confirmed-results columns: 75/150/225/300/370/445), group lines by `round(top/9)`, join wrapped fragments (lowercase-start → no space; trailing "-" → no space), dedupe repeated blocks (lists repeat identically in these PDFs — cut at second `pos=="1"`).

## Gotchas

- admin.html and the tool need **Chrome or Edge** for save-in-place / image paste (File System Access API). Other browsers: use the ⬇ Download buttons and move files manually.
- The folder name `Riders:Teams Database` (displays as "Riders/Teams Database" in Finder) is referenced by relative path from the tool — don't rename either folder.
- Keep `riders.json` and `riders.js` in sync — admin does this automatically; if you edit json by hand, regenerate the js (`window.GLOBAL_RIDERS = <json>;`).
- Old Otley tool untouched at `Otley Elite Open Tool/` — reference only.
