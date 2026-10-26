# Intake And Defaults

Use this reference before calling `start_retirement_checkup`.

## Core Inputs

Collect and validate all of these values:

| Input | Valid value | Handling |
|---|---|---|
| Current age | Integer 18-100 | Do not request an exact birth date. |
| Intended retirement age | Integer 50-80 and not below current age | Ask for a corrected age when invalid. |
| Filing status | `single` or `married` | Use the tool's exact enum. |
| Total retirement savings | Dollars, zero or greater | If itemized accounts are provided, use their sum; the card shows it, and the account list supersedes the total. |
| Current annual income | Dollars, zero or greater | Use primary income only. Keep spouse income separate. |
| Intended annual retirement spending | Dollars, greater than zero | Use annual spending, not monthly spending. |
| Spouse age | Integer 18-100 | Required when filing status is married. |
| Spouse retirement age | Integer 50-80 and not below spouse age | Required when filing status is married. |
| Spouse annual income | Dollars, zero or greater | Required when filing status is married. Pass it as `spouseSalary`, never combined with primary income, and `0` only when the user said the spouse has none. |

Ask for a complete checkup's missing facts in up to three short rounds, at
most three questions in a turn, and say so first: up to three short rounds,
then the plan with every assumption written out. Round one asks the user's age
and the age they would like to stop working, and whether they are married,
with the spouse's age and stop-working age. Round two asks about how much they
have saved, how much each of them earns a year, and how much they would like
to spend each year in retirement, saying rough numbers are fine and today's
spending is a good start. Round three asks for the monthly Social Security
estimate at full retirement age, each spouse's when married, saying it is on
their statement at ssa.gov/myaccount and that they may skip it.

Ask only what is missing: never ask again for a fact the user gave, and skip a
round whose facts are all given or that the user declined, so a request with
nothing left to ask opens the card in the first reply. Say why a question
matters when it is not obvious. Someone already retired stops working at
today's age, with no earnings, so their earnings are not asked, though a
working spouse's still are; when that age is under 50 or over 80, which the
checkup cannot model, say so. When a Social Security estimate is missing, the
first result counts none for that person, and the chat says so. Before a
checkup's first result, never ask about growth, inflation, state, how long to
plan for, account types, or a Roth strategy: they stay on the card as
assumptions.

Do not open the checkup until every applicable core value is known and valid.

## Focused Analysis Facts

The plan facts are current age, planned retirement age, filing status, the
primary person's income, annual spending, and savings; when married, also the
spouse's age, planned retirement age, and income, with 0 only when the user
said so; for a claiming question, also the benefit estimates at full
retirement age. A focused claiming analysis needs the user's own benefit
estimate above 0, or the question uses the explorer instead; a focused
retirement-age analysis needs someone still earning, or explain that no
retirement age is left to compare.

A focused review keeps its button disabled until every plan fact is given. A
value of 0 counts as given; an omitted value does not. Pass the spouse's income
as `spouseSalary`, and `0` only when the user said the spouse has none.

## Optional Inputs

Before the first result, ask only for the Social Security estimates, in the
third round. Never ask for the rest before it: pass a value the user
volunteers; otherwise the default below applies, and the card lists it.

| Input | Valid value | Default or omission effect |
|---|---|---|
| Primary Social Security benefit at full retirement age | Monthly dollars, zero or greater | `$0`; the baseline is incomplete until the user supplies an SSA estimate. |
| Primary claiming age | Integer 62-70 | `67`. |
| Spouse Social Security benefit | Monthly dollars, zero or greater | `$0`. |
| Spouse claiming age | Integer 62-70 | `67`. |
| Primary or spouse pension | Annual dollars, zero or greater | Omitted. |
| State | Two-letter US abbreviation | State income tax is not modeled. Never ask for it before the first result; pass a state the user names, and do not infer it from other information. |
| Itemized accounts | Traditional, Roth, or taxable balances and owner | All savings use one traditional pre-tax bucket. Cash, checking, ordinary savings, money-market, and brokerage balances map to taxable. |
| Primary or spouse life expectancy | Integer 60-110 | Planning horizon age `95`. |
| Expected real return | Decimal -0.10 to 0.15 | `0.02`, or 2% real. |
| Inflation | Decimal 0 to 0.15 | `0.03`, or 3%. |
| Supplemental income | Monthly rental, annuity, or other income with optional start/end ages | None. Do not duplicate salary, Social Security, or pension here. |
| Roth conversion policy | `conservative`, `moderate`, `aggressive`, `optimized`, or `none` | `conservative`. Offer `optimized` when the user wants modeled ACA subsidy and Medicare IRMAA effects included in conversion sizing. |

After the first result, sharpen it one fact a turn, the biggest first, each
asked once and each skippable: a missing Social Security estimate, then a
pension or other regular income in retirement, then, before the Roth decision,
how savings split between pre-tax, Roth and taxable when the user asked about
Roth; each answer opens a fresh card with every fact, and the chat says what
moved only from completed results.

When passing percentages to a tool, convert them to decimals. For example,
pass 2% as `0.02`.

## Material Omission Language

Use direct language when a result depends on a default that materially limits
it:

- No Social Security estimate: "No Social Security estimate was entered, so
  the forecast models a $0 monthly benefit. Add the estimate from your SSA
  statement to make the baseline more complete."
- No account mix: "The forecast treats all savings as one pre-tax account.
  Adding traditional, Roth, and taxable balances would improve withdrawal and
  Roth-conversion modeling."
- No state: "State income tax is not modeled."
- No life expectancy: "The planning horizon defaults to age 95."

Do not describe an omitted material input as if the user supplied it.

## Launcher Contract

1. Pass the user-provided and directly derived values to
   `start_retirement_checkup`. The card shows them, and its assumptions line
   lists every default the forecast applies, such as an expected real return
   of 2%.
2. A complete checkup's card is the read-back: before it opens, say in one
   line that it shows the plan as understood, with what was assumed, and ask
   the user to check it and press **Run my checkup**; do not list every value,
   explain a default, or ask for a confirmation first. A host that shows no
   card gets the facts and the assumptions in two short lines.
3. When a result counts no Social Security, say so, using the language above.
4. If a Roth follow-up is relevant, the card shows the Roth strategy:
   conservative unless the user selected another policy. When the user asks,
   explain that `optimized` considers modeled ACA and Medicare effects.
5. The persistent UI shows the review and requires the user to click
   **Run my checkup**, or, for a focused analysis, that analysis's button.

Do not silently infer financial values. Simple unit conversion or summing
itemized balances is allowed when the card shows the result.
