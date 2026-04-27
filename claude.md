# Claude Instructions for CLDP Reports
## Who I'm Working With
I am working with a non-technical analyst who receives WhatsApp group exports from a programme team, translates them, removes PII, and brings the cleaned data here for analysis. My job is to run the pipeline reliably and produce a publish-ready HTML report — not to over-engineer or add things that weren't asked for. The analyst prefers the Claude Code interface over CLI.

## How I Should Communicate
* **Always explain what I'm doing before I do it.** Never just run a command or create a file without saying what it is and why.
* **Use plain English.** If I must use a technical term, explain it immediately in simple words.
* **The user understands the data and the programme deeply** — she just wants the report generated correctly and consistently.
* **After completing a task, summarize what just happened** in 1–3 plain sentences so she always knows where things stand.
⠀
## About This Project
This project takes a **cleaned, translated WhatsApp group export** and produces a structured **HTML engagement report**, then updates a **GitHub Pages index** that lists all reports by state.
The analyst receives a raw .txt export from the program team. She translates it, masks all phone numbers, and produces a **2-column CSV** (original language | English translation). That CSV is the starting point here.
The pipeline runs 8 steps (elaborated below) and generates two outputs per group: an **Excel file** of the clustered and analysed messages (for the analyst to cross-reference and verify the analysis) and a **single self-contained HTML report**. The report format is templatized and consistent across all states and the Excel file is included in the source data.
**The goal is consistency and speed.** Each new group report should follow the same structure, the same visual template, and the same methodology as the ones before it.

## The 8-Step Pipeline
**1** **Parse** — Read each row using the WhatsApp timestamp format (DD/MM/YY, H:MM am/pm – Sender: Message). Rows without a timestamp are attached to the most recent timestamped entry from the same sender.
**2** **Clean** — Remove system-generated lines (group creation, encryption notices, member additions). Classify remaining messages as text, media, or media+text.
**3** **Cluster** — Merge consecutive messages from the same sender within a 5-minute window into one cluster. This reflects how people actually communicate on WhatsApp.
**4** **Classify senders** — Named senders = Janagraha admins. Phone numbers = Councillors. All phone numbers stay masked.
**5** **Tag themes** — Keyword-match each cluster to one or more themes. Flag spam and exclude from theme analysis.
**6** **Analyse** — Compute: member count, active senders, admin vs. councillor split, top contributors, month-on-month activity, post performance, poll results.
**7** **Generate report** — Output a single self-contained HTML file using the standard template below. In the About section, add the link to the analyzed Excel file as the source data with a report generated date in DD-Month-YYYY format.
**8** **Update index** — Add the new report card to index.html under the correct state.

⠀Member Count Formula
### Total members = unique phone numbers from join/add system messages (deduplicated) + named individuals This is cumulative — members who later left are still counted.

## Ground Rules
**1** **Consistency first.** Every report should look and feel like the ones already published.
**2** **Explain every step.** Tell the user what's happening at each stage of the pipeline.
**3** **No new features unless asked.** Don't add sections, charts, or design changes that weren't requested.
**4** **Check in before judgement calls.** New themes, ambiguous senders, unusual data — ask first.
**5** **Use the existing template exactly.** Layout, typography, section order, and chart library are fixed.
**6** **Keep the index page clean.** All report cards in a single flat flex row; "No reports yet" placeholder for states with no reports.

⠀
## How to Think About Tasks
When the user brings a new group's CSV, follow this pattern:
**1** **Confirm the input** — restate what group it's for, what language, and what date range is covered.
**2** **Run the pipeline** — step by step, narrating what's happening.
**3** **Flag anything unusual** — new themes, low cluster count, missing polls, etc.
**4** **Generate the report** — output the HTML file named CLDP_[STATE]_Engagement_Report.html.
**5** **Update the index** — add the new report to index.html.
**6** **Summarize** — one short paragraph: what the report covers, how many clusters, anything notable.
⠀
## Report Template
* **Layout:** Two-zone page, 1280px max-width, 64px side padding
* **Zone 1 (full width):** Title, subtitle, three numbered methodology notes, source dataset link, report generated date (DD Month YYYY), internal-use disclaimer
* **Zone 2 (two-column):** Sticky TOC at 220px left | 64px gap | main content ~996px right
* **TOC behaviour:** Active section link becomes bold + green underline via IntersectionObserver (uses CSS class `.active`, not inline styles)
* **Section IDs:** #overview #key-themes #sender-analysis #post-performance #poll-analysis #mom-progress #about
* **Section order:** Overview · Key Themes · Sender Analysis · Post Performance · Poll Analysis · Month-on-Month Progress
* **Section dividers:** 1px border-top between each section
* **Charts:** Chart.js
* **Word cloud and themes:** Councillor messages only, not admin content
* **Font:** Manrope (Google Fonts, weights 400/500/600/700/800) — KA and UP have been migrated

### Required elements in Zone 1 (About section)
* Three numbered methodology caveats
* Source data link to the Excel file (e.g. `CLDP_[STATE]_clustered_messages.xlsx`) + report generated date
* Internal-use disclaimer: *"This report is intended for internal team use only. The source data contains phone numbers — please be mindful before sharing the source file externally."*

### Required elements in each section
* **Post Performance:** Below the "Low-impact posts" heading, include this caveat: *"WhatsApp reactions (emoji responses) are not captured in the export unless a member typed it as an explicit reply. Posts in this section may have received acknowledgements or reactions that have not been considered in the analysis."*
* **Month-on-Month Progress:** Before the data table, include a single summary paragraph showing how overall engagement has trended from the group's launch month to the most recent month (grown, plateaued, or declined). Style as a green-light background callout block.

### Cover image
* Each report has a full-width cover image (280px tall) above the `.outer` container, using a locally stored image file named `cover_[state].jpg`
* No icon overlapping the cover — this was tried and removed

### Colour palette
| Variable | Value | Role |
|---|---|---|
| `--green` | `#003049` | Primary / dominant — all interactive elements, TOC, section labels, links |
| `--green-light` | `#FDF0D5` | Light background — KPI strip, poll section, MoM callout, highlight boxes |
| `--green-mid` | `#669BBC` | Steel blue — secondary accent, word cloud xl tags |
| `--navy` | `#780000` | Dark maroon — table headers, Janagraha sender bars, poll winner fill |
| `--navy-light` | `#eef4f8` | Light blue — table row hover only |
| `--red` | `#C1121F` | Bright red — disclaimers, warnings, low-impact caveat text |
| `--text` | `#111827` | Body text |
| `--muted` | `#6b7280` | Secondary text, metadata |

**Favicon:** Navy blue (`#003049`) rounded square with white "J".

**Word cloud tags:** Blue scale — xl `#669BBC`, lg `#99c4d8`, md `#c5dcea`, sm `#e8f1f7`. All text `#003049` or `#4a7a96`.

**Chart.js constants:**
- `GREEN = '#003049'`, `NAVY = '#780000'`
- KA COLORS (8): `['#003049','#669BBC','#C1121F','#780000','#99c4d8','#4a7a96','#e8a0a3','#b3cfd8']`
- UP COLORS (3): `['#003049','#669BBC','#C1121F']`

**Table styling:** Plain — no alternating row colours, no peak/highlight row background. Red (`#780000`) header, subtle blue hover (`#eef4f8`) only. Caveat number circles are always `#003049` (not `var(--navy)`).

**Disclaimers:** Internal-use disclaimer is `13.5px`, `#b91c1c`, `font-weight:500`. Low-impact caveat is `12px`, same colour. Both appear BEFORE the numbered caveats in the About section.

### AI Slop Check — run before finalising any screen
Do not introduce: coloured left borders on KPI cards, gradient text or backgrounds, glassmorphism, dark mode, 3-column hero stat grids with card chrome, all-caps labels on every element, animated counters, or timeline components. KPI stats use a clean top-border rule (2px solid `var(--text)`) on a white background — no card fill, no left border.

⠀Type Scale
| **Role** | **Size / Weight** | **Used for** |
|:-:|:-:|:-:|
| Display | 36px / 800 | Page title |
| H2 | 18px / 700 | Section headers (About) |
| UI | 13px / 700 | State labels, card titles |
| Body | 15px / 400 | Prose, explainer text |
| Small | 12px / 400 | Card meta, dates |
| Eyebrow | 11px / 600 uppercase | Section labels |

## Themes
Roads & Drains · CLDP Programme Engagement · Public Health · Community Programmes · MLA/MP Engagement · Waste Segregation · Rajyotsava · Sanitation Workers · General/Other
Spam messages are flagged and excluded from theme analysis.

## Reports Published So Far
| **File** | **State** | **Period** | **Clusters** | **Generated** |
|:-:|:-:|:-:|:-:|:-:|
| CLDP_KA_Engagement_Report.html | Karnataka | Nov 2025–Apr 2026 | 84 | 23 Apr 2026 |
| CLDP_UP_Engagement_Report.html | Uttar Pradesh | Oct 2025–Apr 2026 | 36 | 23 Apr 2026 |
### Group-Specific Notes
**Karnataka (KA):** 52 members (47 phone numbers + 5 named). Admins: HL Sir Janaagraha, shinjini. 2-column CSV (Kannada + English). 1 poll.
**Uttar Pradesh (UP):** 24 members. Admin: Mehak Janaagraha. Hindi-only (no translation column). Silent Nov–Dec 2025. 3 polls, very low participation (12–33%). No field/ward work updates from councillors.

## Index Page (index.html)
* Container: 1100px max-width, 48px side padding, `#f7f7f5` background
* Header: botanical apple illustration (`usda_apple.png`), 240px tall cover band
* Report cards: flex row, wrap, 340px wide each, 200px image, state name as green eyebrow label, cluster count + generated date in card meta
* No per-state section grid — all cards in a single flat `.report-grid` flex container

## Publishing to GitHub Pages
* All HTML files must be uploaded together
* index.html at the root is the entry point
* Reports listed by state; states with no reports show "No reports yet"
* New report cards are added to index.html each time a new report is generated

## Keeping This File Current
After each new report is generated, update this file to reflect: the new report in the table above, any new group-specific notes, and any new themes added. Use the actual generated files as the source of truth, not memory.