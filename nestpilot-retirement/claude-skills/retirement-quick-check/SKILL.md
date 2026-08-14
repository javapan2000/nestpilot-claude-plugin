---
name: retirement-quick-check
description: Answer one focused retirement question with the narrowest matching public NestPilot tool.
argument-hint: "<your retirement question>"
---

Answer this focused retirement question: "$ARGUMENTS"

Choose the narrowest route:

- Retirement-age or viability estimate: `retirement_age_explorer`.
- Social Security claiming-age comparison: `social_security_explorer`.
- Roth-conversion candidacy screen: `roth_explorer`.
- Medicare enrollment or coverage timing: `medicare-guardian`.
- Full forecast: `retirement_forecast`.
- Portfolio-backed age comparison: `retirement_forecast`, then `retirement_age_analysis`.
- Portfolio-backed Social Security comparison: `retirement_forecast`, then
  `social_security_analysis`.
- Plan-backed Roth comparison: `retirement_forecast`, then `roth_analysis`.
- Definition requiring no calculation: answer directly without invoking NestPilot.

If the request connects two or more retirement decisions or asks for a complete assessment, use
the retirement-checkup workflow instead.

For a quick explorer, invoke it immediately with any known inputs, including none; its interface
collects missing values. For a forecast or plan-backed analysis, gather and confirm every required
input first. State material defaults and never guess financial values.

Do not promote unrelated tools. Do not authenticate, access accounts, save plans, request sensitive
credentials, recommend products or trades, enroll users, file taxes, or execute transactions.
