---
name: retirement-checkup
description: Run an anonymous NestPilot retirement checkup in the checkup card, with a confirmed review and only relevant follow-up analyses.
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

1. Gather missing core inputs together: current age, intended retirement age, filing status,
   total retirement savings, current annual income, and intended annual retirement spending.
   For a married household, also gather spouse age and intended retirement age.
2. Invite optional high-impact inputs without blocking: Social Security benefit estimates and
   claiming ages, pension income, state, account mix, spouse income, supplemental income, life
   expectancies, expected real return, and inflation.
3. Echo the captured values, separate user-provided values from defaults, state that the default
   expected return is 2% real, explain material omissions, and ask for confirmation. Never guess
   financial values.
4. After confirmation, call `start_retirement_checkup` once with the facts, the requested
   analyses (`retirement_age`, `social_security`, `roth`: only those the request connects), and
   the Roth policy: `conservative` unless the user chooses `optimized`, which also models ACA
   subsidy and Medicare IRMAA effects. The launcher performs no calculation.
5. Tell the user to review the displayed inputs and defaults and click **Run my checkup**.
   The checkup is one card that moves forward: it shows the plan as understood, then each
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
8. If a fact changes before calculation, launch again with the corrected facts. After a
   calculation, start a fresh checkup so dependent results are not mixed.

Do not run every analysis merely because it is available. Do not authenticate, access accounts,
save plans, create handoffs, request sensitive credentials, recommend products or trades, enroll
users, file taxes, or execute transactions. Describe results as educational modeled estimates.
