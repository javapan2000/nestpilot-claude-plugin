---
name: nestpilot-retirement-checkup
description: Coordinate an anonymous educational retirement checkup when a user asks whether they can retire or wants retirement age, Social Security, Roth conversion, healthcare, or Medicare decisions analyzed together. Do not use for a single definition, product selection, transactions, account access, or plan saving.
---

# NestPilot Retirement Checkup

Open the appropriate interactive NestPilot experience. Model-visible tools only
launch a review UI. Financial calculations run only after the user explicitly
clicks the corresponding button in that UI.

## Routing Precedence

Classify the whole request before selecting a tool.

- A complete or holistic retirement checkup, or any request connecting two or
  more retirement decisions, uses `start_retirement_checkup`.
- One standalone Social Security timing question uses
  `open_social_security_explorer`.
- One standalone Roth conversion candidacy question uses
  `open_roth_explorer`.
- One standalone retirement-age question uses
  `open_retirement_age_explorer`.
- A Medicare timing question uses `medicare-guardian`.
- A definition or general educational question may be answered without a tool.

This whole-request classification takes priority over topic keywords. Never
open multiple focused explorers for a complete checkup.

## Complete Checkup Workflow

1. Read [intake-and-defaults.md](references/intake-and-defaults.md). Ask for
   all missing core inputs together. Do not guess financial values.
2. Identify the connected decisions requested. Use these exact
   `requestedAnalyses` values:
   - `retirement_age`
   - `social_security`
   - `roth`
   Use [adaptive-routing.md](references/adaptive-routing.md) to include only
   relevant requested analyses.
3. Use Roth policy `conservative` unless the user explicitly chooses
   `optimized`. Explain that optimized includes modeled ACA subsidy effects
   before Medicare and Medicare IRMAA effects where applicable.
4. Call `start_retirement_checkup` once with the collected facts, requested
   analyses, and Roth policy.
5. Tell the user that no calculation ran from the launcher. Ask them to review
   the displayed inputs and defaults and click **Run baseline forecast**.
6. Do not call any calculator from the conversation. The persistent widget
   calls the app-only baseline and follow-up calculators only after explicit
   clicks, offers one next analysis at a time, retains every completed result
   as a tab, and keeps the web-planner continuation.
7. When the widget reports a completed step through model context, summarize
   only values grounded in that completed result. Do not invent missing
   financial numbers. Use [decision-snapshot.md](references/decision-snapshot.md)
   for the compact response.

If an input changes before calculation, call `start_retirement_checkup` again
with the corrected facts. If an input changes after calculations, explain that
the user should start a fresh checkup so dependent results are not mixed.

## Focused Explorer Workflow

For a single focused question, call exactly one matching `open_*` launcher
with any known inputs. The launcher never calculates. Tell the user to review
the form and click its calculation button.

Do not call `social_security_explorer`, `roth_explorer`, or
`retirement_age_explorer` from the conversation. Those are app-only
calculators retained for widget calls and backward compatibility.

## Medicare

Call `medicare-guardian` with any known birth month/year, employment,
coverage, employer-size, HSA, and creditable-drug-coverage hints. The interface
collects missing values and calls its app-only calculator after the user
clicks. Use only birth month and year; never request an exact birth day. If the
user volunteers that they were born on the 1st of the month, pass
`bornOnFirst: true` — Medicare's one day-dependent rule shifts their eligibility
month and IEP one month earlier.

Do not compare, recommend, sell, or enroll the user in Medicare plans.

## Allowed Model-Visible Tools

Use only these tools from the conversation:

- `start_retirement_checkup`
- `open_social_security_explorer`
- `open_roth_explorer`
- `open_retirement_age_explorer`
- `medicare-guardian`

Do not call or name app-only calculators, render helpers, handoff helpers, or
tools outside this allowlist. In particular, never call
`retirement_forecast`, `retirement_age_analysis`,
`social_security_analysis`, `roth_analysis`, `roth_simulate`,
`roth_optimize`, `render_retirement_checkup`,
`create_plan_handoff`, or `medicare-analyze` from the conversation.

## Failure Rules

- Missing or invalid core input: continue intake without calling a launcher.
- Launcher failed: retain the user's facts, explain that the UI did not open,
  and offer to retry the same launcher.
- App calculation failed: keep prior valid tabs and do not invent a result.
- Missing baseline `planInput`: dependent app-only analyses must not run.
- Unsupported request: do not invoke NestPilot tools.

## Boundaries

- Treat every result as an educational estimate, not financial, investment,
  tax, legal, medical, or benefits advice.
- Do not recommend products, investments, trades, advisors, or Medicare plans.
- Do not file taxes, apply for benefits, enroll in coverage, move money, send
  messages, or execute transactions.
- Do not request an exact birth date, Social Security number, credentials,
  password, MFA code, or email address.
- Do not authenticate users, access accounts, or save plans. The results UI may
  create a temporary, 24-hour, single-use planner handoff only after the user
  explicitly clicks **Continue in web planner**.
- Say "the modeled result shows" rather than "you should."
