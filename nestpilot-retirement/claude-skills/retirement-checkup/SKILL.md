---
name: retirement-checkup
description: Run an anonymous NestPilot retirement checkup with a confirmed baseline and only relevant follow-up analyses.
argument-hint: "[current age] [single|married]"
---

Run a complete, anonymous NestPilot Retirement Checkup. Arguments, when supplied: $ARGUMENTS

1. Gather missing core inputs together: current age, intended retirement age, filing status,
   total retirement savings, current annual income, and intended annual retirement spending.
   For a married household, also gather spouse age and intended retirement age.
2. Invite optional high-impact inputs without blocking: Social Security benefit estimates and
   claiming ages, pension income, state, account mix, spouse income, supplemental income, life
   expectancies, expected real return, and inflation.
3. Echo the captured values, separate user-provided values from defaults, state that the default
   expected return is 2% real, explain material omissions, and ask for confirmation. Never guess
   financial values.
4. After confirmation, call `retirement_forecast`. Present the primary readiness result and every
   material `assumptionsApplied` entry. Retain the returned `planInput`.
5. Select only one next follow-up justified by the request, facts, and completed results:
   - `retirement_age_analysis` for retirement-age comparisons or timing uncertainty.
   - `social_security_analysis` for claiming or breakeven questions with the required benefit data.
   - `roth_analysis` for Roth questions or a supported lower-income conversion window.
   - `roth_simulate` only for an explicitly requested custom policy.
   - `roth_optimize` only for an explicit maximize-legacy objective.
   - `medicare-guardian` when the user is at least 60 or explicitly asks about Medicare; let its
     interface collect the required inputs and invoke `medicare-analyze`.
6. Explain what decision the proposed follow-up would clarify and ask for explicit confirmation.
   Do not call it in the proposal turn. Confirmation authorizes only that one analysis.
7. After an approved follow-up completes, present its material result and assumptions before
   proposing one next analysis. Never execute two analytical tools in one assistant turn. Pass
   `planInput` unchanged to plan-backed follow-ups. If an assumption changes, rerun the baseline
   and every dependent analysis that remains relevant.
8. When the user declines, asks to stop, or completes every relevant approved follow-up, call
   `render_retirement_checkup` automatically as the final tool without asking for separate render
   confirmation. Include the baseline and every successful follow-up in execution order, copying
   each tool's `structuredContent` unchanged into its section result.
9. Return five compact sections: Readiness, Assumptions, Three trade-offs, Uncertainty, and Next
   refinement. Use only confirmed inputs and completed tool results.

Do not run every tool merely because it is available. Do not authenticate, access accounts, save
plans, create handoffs, request sensitive credentials, recommend products or trades, enroll users,
file taxes, or execute transactions. Describe results as educational modeled estimates.
