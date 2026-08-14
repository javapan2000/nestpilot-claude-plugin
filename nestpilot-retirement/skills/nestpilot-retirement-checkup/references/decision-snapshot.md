# Decision Snapshot

Use this after the widget reports a completed baseline or follow-up through model
context. The persistent widget remains the source of truth for full results.

Return only compact, result-grounded sections that are useful at that moment:

1. **Modeled result** — the completed step's primary returned outcome.
2. **Assumptions** — material supplied facts and defaults visible in the result.
3. **Trade-off** — one decision implication supported by the completed result.
4. **Uncertainty** — one limitation that could materially change the result.
5. **Next UI action** — name the one button currently offered by the widget, or
   say that the requested steps are complete.

Do not invent values, summarize a step that has not completed, or call another
calculator from the conversation. If a calculation fails, say that no result
was produced and leave prior valid tabs intact.
