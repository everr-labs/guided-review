# Guided Review Diff Visibility Design

## Context

The guided-review skill currently prefers clean excerpts from the post-change file and reserves unified diff hunks for cases where the exact edit shape is important. That keeps reviews readable, but it can hide the old behavior when the review risk depends on what changed.

## Goal

Make semantic before/after changes more visible during guided PR reviews without turning the session into a full diff dump.

## Proposed Behavior

For meaningful semantic changes, the skill should prefer a trimmed unified diff first when the old behavior is needed to understand the review risk. Clean post-change excerpts should remain available as supporting context, but they should not replace the diff when the before/after contrast is the clearest way to review the change.

Each relevant section should present the code in this order:

1. A trimmed unified diff hunk showing the changed lines plus enough nearby context to understand the behavior.
2. A brief explanation of what changed.
3. A brief explanation of why it matters or what risk it affects.
4. Any concrete concern, labeled with severity.

## When To Prefer Diffs

Use trimmed unified diffs for semantic changes involving:

- validation or guard conditions
- permissions, authorization, or access control
- defaults, fallbacks, or configuration behavior
- data flow, state updates, or persistence behavior
- error handling, retries, or failure paths
- deleted branches or removed behavior
- API shape or contract changes
- migrations or schema changes
- tests whose assertions changed meaningfully

## When To Avoid Diffs

Still collapse or summarize low-signal churn:

- formatting-only edits
- generated output
- broad mechanical renames
- imports or ordering changes with no behavior impact
- repeated edits where one representative hunk explains the pattern

## Section Shape

The section format should preserve the guided-review pacing:

```diff
path/to/file.ts

- previous guard, branch, assertion, or behavior
+ new guard, branch, assertion, or behavior
  nearby context needed to review the risk
```

Then follow with concise prose:

- `What changed`: one or two concrete sentences.
- `Why it matters`: the behavior, risk, or invariant affected.
- `Concern`: only when there is a real review point.

## Non-Goals

- Do not dump the complete PR diff by default.
- Do not make every section diff-first when the post-change code is enough.
- Do not remove the ability to show pristine final-code excerpts.
- Do not change the false-positive verification workflow.

## Success Criteria

- Review sections make old behavior visible when it is necessary to understand risk.
- The skill still filters obvious noise and avoids exhaustive file-by-file narration.
- A user can compare the before/after behavior without asking for the missing old code.
