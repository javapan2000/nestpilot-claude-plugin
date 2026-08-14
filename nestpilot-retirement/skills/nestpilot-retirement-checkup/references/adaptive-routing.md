# Adaptive Routing

The model selects requested analyses before opening the persistent Retirement
Checkup. The widget then runs only one app-only calculation per explicit user
click and preserves every completed result as a tab.

## Selection Order

The widget offers requested follow-ups in this deterministic order:

1. Retirement age.
2. Social Security claiming.
3. Roth conversion strategy.

Use `requestedAnalyses` only for decisions the user asked to connect. A
general complete checkup may include all three. The widget always runs the
baseline first.

## Retirement Age

Include `retirement_age` when the user asks when they can retire, provides
multiple candidate ages, or wants retirement timing connected to the forecast.

## Social Security

Include `social_security` when the user asks about claiming ages, breakeven
timing, spouse coordination, or portfolio withdrawals around Social Security.
Invite the monthly benefit estimate at full retirement age before launching.

## Roth Conversion

Include `roth` when the user asks about a conversion window or connects
pre-tax savings, retirement, Social Security, ACA coverage, or Medicare.

Use `rothPolicy: conservative` unless the user explicitly selects
`optimized`. Optimized models all-in marginal costs including ACA subsidy
effects before Medicare and Medicare IRMAA effects where applicable. Never
silently substitute moderate.

## Medicare

Medicare remains a focused widget flow launched with `medicare-guardian`.
Do not mix its calculator into the plan-backed checkup workflow. For a user
younger than 60 who did not ask about Medicare, do not launch it.

## Changed Assumptions

Before calculation, relaunch the checkup with corrected inputs. After any
calculation, start a fresh checkup so prior results are not combined with a new
baseline.

## Persistent Results

The one checkup widget retains the baseline and every completed follow-up as
tabs. It also retains the optional web-planner CTA. The conversation never
calls analysis or render tools directly.
