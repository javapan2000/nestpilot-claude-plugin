---
name: retirement-checkup
description: Run an anonymous NestPilot retirement checkup in the checkup card, asking a few questions at a time, with only relevant follow-up analyses.
argument-hint: "[current age] [single|married]"
---

Run a complete, anonymous NestPilot Retirement Checkup. Arguments, when supplied: $ARGUMENTS

A complete checkup, or any request connecting two or more retirement decisions, uses
`start_retirement_checkup` without `focus`. One retirement-age or Social Security claiming question
uses `start_retirement_checkup` with `focus` when the user has given the plan facts, or asks for
their plan, portfolio, spending, taxes, or a couple's joint claiming to be modeled; ask for any
missing plan facts first. The same question with only a few facts and no request for plan modeling
uses the matching explorer, `open_retirement_age_explorer` or `open_social_security_explorer`;
after an explorer result, offer the focused analysis once, naming the facts it needs, and never
open it unasked. The plan facts are current age, planned retirement age, filing status, the primary
person's income, annual spending, and savings; when married, also the spouse's age, planned
retirement age, and income, with 0 only when the user said so; for a claiming question, also the
benefit estimates at full retirement age. A focused claiming analysis needs the user's own benefit
estimate above 0, or the question uses the explorer instead; a focused retirement-age analysis
needs someone still earning, or explain that no retirement age is left to compare. A Roth
conversion candidacy question uses `open_roth_explorer`; plan-backed Roth modeling runs as the Roth
step of a retirement checkup, which `start_retirement_checkup` opens without `focus`.

For a complete checkup:

1. The core facts are current age, intended retirement age, filing status, total retirement savings,
   current annual income, and intended annual retirement spending; for a married household also the
   spouse's age, intended retirement age, and income. Ask for a complete checkup's missing facts in
   up to three short rounds, at most three questions in a turn, and say so first: up to three short
   rounds, then the plan with every assumption written out. Round one asks the user's age and the
   age they would like to stop working, and whether they are married, with the spouse's age and
   stop-working age. Round two asks about how much they have saved, how much each of them earns a
   year, and how much they would like to spend each year in retirement, saying rough numbers are
   fine and today's spending is a good start. Round three asks for the monthly Social Security
   estimate at full retirement age, each spouse's when married, saying it is on their statement at
   ssa.gov/myaccount and that they may skip it. Never guess financial values.
2. Ask only what is missing: never ask again for a fact the user gave, and skip a round whose facts
   are all given or that the user declined, so a request with nothing left to ask opens the card in
   the first reply. Say why a question matters when it is not obvious. Someone already retired stops
   working at today's age, with no earnings, so their earnings are not asked, though a working
   spouse's still are; when that age is under 50 or over 80, which the checkup cannot model, say so.
   When a Social Security estimate is missing, the first result counts none for that person, and the
   chat says so. Before a checkup's first result, never ask about growth, inflation, state, how long
   to plan for, account types, or a Roth strategy: they stay on the card as assumptions.
3. Then call `start_retirement_checkup` once with the facts, the requested analyses
   (`retirement_age`, `social_security`, `roth`: only those the request connects), and the Roth
   policy: `conservative` unless the user chooses `optimized`, which also models ACA subsidy and
   Medicare IRMAA effects. The launcher performs no calculation.
4. A complete checkup's card is the read-back: before it opens, say in one line that it shows the
   plan as understood, with what was assumed, and ask the user to check it and
   press **Run my checkup**; do not list every value, explain a default, or ask for a confirmation
   first. A host that shows no card gets the facts and the assumptions in two short lines. The
   card's assumptions line lists every default, such as an expected return of 2% real.
5. The checkup is one card that moves forward: it shows the plan as understood, then each
   calculation runs only on its own click and the card becomes the verdict, the next decisions, and
   one decision's answer; the full-screen view keeps every completed result as a tab. It keeps
   **Continue in the NestPilot planner**. When the user's message repeats one of the card's
   questions, the answer is already on screen: summarize that result and do not call a tool again.
6. Never call a calculator from the conversation, including `retirement_forecast`,
   `retirement_age_analysis`, `social_security_analysis`, `roth_analysis`, `roth_simulate`,
   `roth_optimize`, `render_retirement_checkup`, or `medicare-analyze`. `medicare-guardian`
   answers only a Medicare enrollment question the user asked; never suggest it, launch it from
   age alone, or offer it as a next step. Its interface collects the inputs.
7. When the view reports a completed step, summarize only values grounded in that result, in the
   same four short parts in every host (ChatGPT and Claude say the same thing — DES-0194 J3):
   1. **Modeled result** — one sentence with the completed step's main outcome, naming the one
      assumption it depends on most.
   2. **One trade-off** — the decision implication the completed result supports.
   3. **One uncertainty** — the limitation most likely to change the result.
   4. **Next step** — the one button the view now offers, or the one question that continues; or
      say that the requested steps are complete.

   Plain words: no raw keys, JSON or unformatted decimals, and no "FRA", "Δ" or "HC Bridge" — the
   precise term may follow in parentheses. Never suggest Medicare as a next step. Do not invent
   values or summarize a step that has not completed; if a calculation fails, say that no result
   was produced.
8. After the first result, sharpen it one fact a turn, the biggest first, each asked once and each
   skippable: a missing Social Security estimate, then a pension or other regular income in
   retirement, then, before the Roth decision, how savings split between pre-tax, Roth and taxable
   when the user asked about Roth; each answer opens a fresh card with every fact, and the chat says
   what moved only from completed results.
9. If a fact changes before calculation, launch again with the corrected facts. After a
   calculation, start a fresh checkup so dependent results are not mixed.

Do not run every analysis merely because it is available. Do not authenticate, access accounts,
save plans, create handoffs, request sensitive credentials, recommend products or trades, enroll
users, file taxes, or execute transactions. Describe results as educational modeled estimates.
