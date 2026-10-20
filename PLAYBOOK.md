# How I run an AI engineering team

I'm a project manager, not a career engineer. On MenuVision I led AI agents the way I'd lead a product team: clear roles, an independent check, written decisions, and one person who owns the calls.

## The team

- **Builder (Claude Code):** writes and integrates the code, runs the tests, and keeps the docs, the decision log and the launch plan current.
- **Auditor (a separate OpenAI model):** reviews each round of changes with no stake in them. It also rehearsed my phone tests on the simulator, checked App Store readiness, dry-ran production database changes on a throwaway copy, and researched the law.
- **Me:** set direction and priorities, and make the calls the agents can't: what a warning should say, what users pay, what we launch with, what we refuse to ship. I test each build on my iPhone and approve every production change: database migrations, server deploys, builds and the App Store submission.

## The rules

1. **Every change gets an adversarial review.** Nothing merges on the builder's word. A finding counts only when the auditor reproduces it with a runnable test.
2. **Severity decides what happens next.** Launch blockers get fixed before launch, every time. Polish gets written down for the first update.
3. **Fix the class, never the example.** A fix that only works for the menu that exposed the bug is rejected. Every bug becomes a permanent test, plus tests for its siblings.
4. **Write the decision down.** What was decided, why, and what was rejected. MenuVision has 175 of them.
5. **Rehearse production changes.** Every database change has a written rollback, rehearsed on a local copy before it touches production.

## Severity

- **P0, blocks launch:** a missed allergy.
- **P1:** a false or overstated warning a normal user would see.
- **Polish:** written down for the first update.

## The auditor's brief, as a template

A starting point distilled from how I ran the audits. Swap in your own product and your own P0.

```text
You are the independent auditor for [product]. You didn't write this change
and you have no stake in it.

Your job: find what would hurt a real user.
- P0 (blocks launch): [the worst failure, e.g. a missed allergy]
- P1: [the next worst, e.g. a false or overstated warning a normal user would see]
- Everything else is polish. List it; don't block on it.

For each finding:
1. Reproduce it with a runnable test. No test, no finding.
2. Say whether the fix needs to cover a class of cases, and name the siblings.
3. Give the severity and why.

End with a verdict: GO or NO-GO, and the findings that decide it.

The change: [branch, diff or summary]
```

## What I'd tell another PM

- AI is a strong engineering team if you lead it like one: a clear brief, an independent check, and a human who owns the decisions.
- Most of the serious bugs came from the auditor, not the builder.
- Written decisions turn "why does it work this way?" into a one-minute answer instead of a day.
