# Project brief: Sales Tracking System

A reference write-up of the project for portfolio, interview and résumé use. All figures that describe the sample portfolio are generated; the facts about scope are the real ones.

## One-line summary

I rebuilt a Google Sheets sales tracking system across six real estate development projects and nearly 1,000 units, standardizing every project's deal pipeline and adding a dashboard of unit counts and total values by project and sales status.

## Résumé wording

> Rebuilt and implemented a Google Sheets sales tracking system across 6 projects and nearly 1,000 units, using Claude and Gemini to standardize deal pipelines and add a dynamic dashboard of unit counts and total values by project and sales status.

## Context

- **Setting:** marketing analytics and operations for a New York real estate developer with several condominium projects selling at the same time.
- **Users:** the sales, marketing, project and accounting teams, who all need the same answer to "where does each unit stand?"
- **Starting point:** one tracker per project, each with its own layout and its own status wording.

## The problem in detail

1. **Statuses did not match.** The same stage had different names in different files, so nothing could be counted across projects without translating first.
2. **Totals were hard to check.** A summary number is only trustworthy when it is computed from the rows under it.
3. **No portfolio view.** A cross-project question meant opening every file.
4. **No view over time.** The trackers showed today's status but not how sales were pacing month by month.

## Approach

### 1. Standardize the source

- One row per unit, the same columns in the same order in every project.
- One status vocabulary with six values, enforced with a dropdown: Available, Reserved, Contract Out, Executed Contract, Confirmed to Close, Closed.
- Dates kept as real dates and prices as numbers, so they can be counted and summed.
- A single tab, the Deal Pipeline, is the only place updates are entered.

### 2. Compute, never type

- Each tracker has a Dashboard tab made only of formulas: `COUNTIF` for units in each status, `SUMIFS` for their total value.
- Changing a unit's status or price changes every total that depends on it, with no second step.

### 3. Roll up across projects

- Because every tracker has the same shape, one set of logic works for all of them.
- The portfolio dashboard stacks the projects into one table and reports units and value by project and status, plus contracts signed and closings by month.

### 4. Make it explorable

- The web version adds interaction that a spreadsheet tab does not offer: pick a project, filter by status, switch between units and value, click a month to see the units behind it.
- Each project opens into its own view with monthly progress, cumulative progress against total inventory, a stacking plan by floor and line, and closings by month.

## Architecture

| Layer | What it is | Who touches it |
|---|---|---|
| Source | Google Sheets tracker per project, Deal Pipeline tab | Sales and operations enter updates |
| Roll-up | Formulas in the sheet, or a read of each tracker by the web dashboard | Nobody: it is computed |
| Output | Dashboard tab in each sheet, and a portfolio web dashboard | Everyone reads |

How the two outputs stay current:

- **In-sheet dashboard:** recalculates the moment a row changes, because it is formulas in the same file.
- **Web dashboard:** reads each tracker when the page is opened or refreshed. It stores no numbers of its own.

## Definitions

| Term | Definition |
|---|---|
| Signed | A unit whose status is Executed Contract, Confirmed to Close or Closed |
| Signed value | Sum of sale prices of signed units |
| Sell-through | Signed units divided by all units in the project |
| Contract Out | Contract sent to the buyer but not yet executed. Not counted as signed. |
| Closing date | Actual date for closed units, target date for units under contract |

## Design decisions and why

- **Status as a dropdown, not free text.** Counting by status only works if the status is spelled the same way every time.
- **Contract Out is not "signed".** A contract that has gone out can still fall through, so it is reported on its own.
- **One row per unit, no section headers inside the data.** Section headers and subtotals inside a table break formulas and exports. Grouping is done in the dashboard instead.
- **The dashboard never stores numbers.** If the dashboard and the tracker can disagree, people stop trusting both.
- **Month views use the contract-signed date.** It is the date that marks a sale, and it is filled for every signed unit.

## My role

- Designed the standard schema and status vocabulary.
- Rebuilt each project's tracker on that schema.
- Built the formula-driven dashboards and the portfolio roll-up.
- Used Claude and Gemini to speed up the standardization and to build the interactive dashboard.

## Tools

Google Sheets (data validation, `COUNTIF`, `SUMIFS`), HTML, CSS, JavaScript, D3.js, Claude, Gemini.

## About the sample in this repository

- Six invented projects, 983 units, with generated prices and dates.
- In the sample, 633 units are signed (64% sell-through) for $678.4M in signed value. These numbers describe the sample only.
- `index.html` embeds the sample rows and computes every figure in the browser from those rows.
- `data/trackers/` holds the six sample trackers as spreadsheets, each with the Deal Pipeline, Dashboard and Status list tabs.

## Questions I can speak to

- **How do the tracker and the dashboard stay in sync?** They cannot drift, because the dashboard is computed from the tracker rows every time. There is one place to enter data and no place to enter a total.
- **What made a portfolio view possible?** Standardizing first. Once every project used the same columns and the same six statuses, the same logic applied everywhere.
- **Why Google Sheets and not a database?** The teams already worked there, several people edit at once, and it needs no new tool or training. The standard schema is what gives it the reliability of a database table.
- **What would I do next?** Connect the web dashboard to the live sheets for every viewer, add list price alongside contract price to track discounting, and add alerts for target closing dates that have passed.
