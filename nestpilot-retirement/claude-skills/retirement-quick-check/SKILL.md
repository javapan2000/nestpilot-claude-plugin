---
name: retirement-quick-check
description: Answer one focused retirement question with the matching NestPilot launcher, a focused analysis of the user's plan or a quick explorer.
argument-hint: "<your retirement question>"
---

Answer this focused retirement question: "$ARGUMENTS"

Classify the whole request first, then choose the route:

- A complete checkup, or any request connecting two or more retirement decisions, uses
  `start_retirement_checkup` without `focus`.
- One retirement-age or Social Security claiming question uses `start_retirement_checkup` with
  `focus` when the user has given the plan facts, or asks for their plan, portfolio, spending,
  taxes, or a couple's joint claiming to be modeled; ask for any missing plan facts first.
- The same question with only a few facts and no request for plan modeling uses the matching
  explorer, `open_retirement_age_explorer` or `open_social_security_explorer`; after an explorer
  result, offer the focused analysis once, naming the facts it needs, and never open it unasked.
- Roth-conversion candidacy screen: `open_roth_explorer`.
- Medicare enrollment or coverage timing: `medicare-guardian`.
- Definition requiring no calculation: answer directly without invoking NestPilot.

The plan facts are current age, planned retirement age, filing status, the primary person's
income, annual spending, and savings; when married, also the spouse's age, planned retirement age,
and income, with 0 only when the user said so; for a claiming question, also the benefit estimates
at full retirement age. A focused claiming analysis needs the user's own benefit estimate above 0,
or the question uses the explorer instead; a focused retirement-age analysis needs someone still
earning, or explain that no retirement age is left to compare.

Every launcher performs no calculation. Pass the known facts, then tell the user to review the
displayed inputs and click the calculation button; the app runs the calculation on that click. For
a focused analysis, pass `focus` as `retirement_age` or `social_security`. Never call an app-only
calculator from the conversation, including `retirement_forecast`, `retirement_age_analysis`,
`social_security_analysis`, `roth_analysis`, `retirement_age_explorer`,
`social_security_explorer`, or `roth_explorer`. State material defaults and never guess financial
values.

Do not promote unrelated tools. Do not authenticate, access accounts, save plans, request sensitive
credentials, recommend products or trades, enroll users, file taxes, or execute transactions.
