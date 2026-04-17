---
name: guided-review
description: Use when manually reviewing a GitHub pull request with the assistant, especially when the user wants noisy changes collapsed, the diff grouped into meaningful sections, brief code-aware explanations, and pauses between sections for questions or PR comments.
---

# Guided Review

## Overview

Turn a GitHub pull request into a guided conversation. Filter noise, group the meaningful changes into sections, review one section at a time, and pause after each section so the user can ask questions or publish PR comments.

Show the relevant code directly. Prefer clean excerpts of the final code over raw diff hunks. Use a diff only when the exact edit shape is itself important to the review point.

Do not dump the full diff or a one-shot summary unless the user explicitly asks for that instead.

## Workflow

1. Access the PR using whatever GitHub path is available in the current environment.
If the PR cannot be accessed, say what is missing and stop.

2. Build a small set of sections based on intent rather than filenames alone.
Good section boundaries include API shape, data flow, behavior changes, tests, migrations, cleanup, or similar semantic groupings.

3. Down-rank or collapse obvious noise.
Examples: formatting-only edits, generated output, bulk renames with no behavior change, or other low-signal churn.
Do not let noise reduction turn the review into prose-only summary. Keep the important code visible.

4. Start the session with a terse map of the review.
List the planned sections in one line each, then begin with the first section.

## Section Format

For each section:

- show the relevant code directly
- default to pristine code excerpts from the post-change file
- use unified diff hunks only when the exact edit, deletion, or line movement matters
- trim unrelated lines, but keep the excerpt verbatim and self-contained enough to review
- briefly explain what changed
- briefly explain why it matters or what behavior it affects
- mention only plausible bugs or improvements that are useful to call out
- label each concern with a severity such as `high`, `medium`, or `low`

Keep explanations brief and concrete. Preserve enough code context for real review. A section should feel like code review, not like a narrated summary of code the user cannot see.

## Interaction Contract

After presenting a section, stop and wait.

While paused, the user may:

- ask questions about the section
- ask for more or less detail
- ask to publish a PR comment
- move to the next section
- jump back to an earlier section

Do not advance automatically after a section. Keep the review paced by the user.

## Comment Publishing

When the user asks to publish a comment:

1. **Draft the comment.** Turn the current review point into a concise PR-ready comment. Keep it grounded in the current section.

2. **Check for similar existing comments.** Before publishing, fetch existing review comments on the PR (inline comments and top-level comments). Compare the drafted comment against them:
   - Skip if an existing comment already covers the same file, function, or line range with substantially the same observation.
   - If a related comment exists but the new point adds meaningfully different detail, note the existing comment and extend or differentiate rather than repeating.
   - If no similar comment is found, proceed to publish.

3. **Publish or report.**
   - If publishing: use the best available GitHub capability in the environment. Confirm briefly what was posted.
   - If skipping due to similarity: tell the user which existing comment already covers the point and quote or summarize it, so the user can decide whether to post anyway.

4. **Stay in position.** Remain at the same review section after publishing unless the user asks to move on.

The comment flow is part of the same review conversation, not a separate mode.

## Guardrails

- prioritize semantic changes over mechanical churn
- prefer a few meaningful sections over exhaustive file-by-file narration
- do not replace the key code with paraphrase when the code itself is what needs review
- if a review point depends on code, show that code cleanly before explaining it
- prefer small pristine excerpts over noisy hunks, but prefer noisy hunks over hiding the code entirely
- avoid speculative, low-confidence, or low-value findings
- if a section is mostly noise, say so briefly and compress it
- optimize for signal, pacing, and usefulness over completeness theater

End each section by explicitly waiting for the user, for example:
`Questions on this section, or should I move to the next one?`
