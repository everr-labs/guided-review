---
name: guided-review
description: Use when the user wants to manually review code step-by-step by splitting the review into sections with pauses between each section.
---

# Guided Review

## Overview

Turn a code review into a guided conversation.

The source can be a GitHub pull request or just a local branch.

Filter noise, group the meaningful changes into sections, review one section at a time, and pause after each section so the user can ask questions or publish PR comments.

Use simple, beginner-friendly language throughout the review. Favor explanations that a junior developer, or a developer new to the codebase, can understand without knowing project-specific jargon.

Do not dump the full diff or a one-shot summary unless the user explicitly asks for that instead.

## Workflow

1. If reviewing a Github PR, fetch content locally and use Git for review data.

2. Build a small set of sections based on intent rather than filenames alone.
Good section boundaries include API shape, data flow, behavior changes, migrations, cleanup, or similar semantic groupings.

3. Down-rank obvious noise, but do not hide small changes just because they look cosmetic.
Safe-to-collapse examples include generated output, broad mechanical renames with no behavior change, import ordering, and repeated edits where one example explains the pattern. Formatting-only edits and small cosmetic fixes are not automatically noise. Show them when they affect user-visible text, layout, docs, test readability, naming clarity, or how easy the code is to understand.
Do not let noise reduction turn the review into prose-only summary. Keep the important code visible.

4. Start the session with a terse map of the review. and ask the user if we should skip any before showing the first one.

5. Begin with the first section

## Section Format

For each section:

- start with an explaination of what's included in this section, with a list of files we are going to review
- show the code
- briefly explain what changed, why it matters or what behavior it affects
- mention only plausible bugs or improvements that are useful to call out
- label each concern with a severity such as `high`, `medium`, or `low`
- for each section show a summary of the test coverage and report the uncovered business logic as scenario list, each one with a severity label

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
   - Inline comments should state the concrete code concern or question directly. Avoid labels, summaries, or meta-commentary such as "Inline question about test coverage".
   - If the user asks for a terse comment, keep it terse without stripping the specific code context.

2. **Check for similar existing comments.** Before publishing, fetch existing review comments on the PR (inline comments and top-level comments). Compare the drafted comment against them:
   - Skip if an existing comment already covers the same file, function, or line range with substantially the same observation.
   - If a related comment exists but the new point adds meaningfully different detail, note the existing comment and extend or differentiate rather than repeating.
   - If no similar comment is found, proceed to publish.

3. **Always preview before publishing.** Show every visible thing that will be posted: the inline comment body, where it will be posted (file and line range, or top-level), and any review summary/top-level body required by the publishing tool. Wait for explicit confirmation. Do not post until the user approves. If the user requests edits, revise and preview again.

4. **Publish or report.**
   - If publishing inline comments: prefer a GitHub capability that does not create a top-level review body. If the available tool requires one, make it substantive and user-approved, not a duplicate label or bookkeeping sentence.
   - If publishing (after approval): use the best available GitHub capability in the environment. Confirm briefly what was posted.
   - If skipping due to similarity: tell the user which existing comment already covers the point and quote or summarize it, so the user can decide whether to post anyway.

5. **Stay in position.** Remain at the same review section after publishing unless the user asks to move on.

The comment flow is part of the same review conversation, not a separate mode.

## Guardrails

- prioritize semantic changes over mechanical churn
- prefer a few meaningful sections over exhaustive file-by-file narration
- do not replace the key code with paraphrase when the code itself is what needs review
- if a review point depends on code, include enough surrounding code to understand it before explaining it
- prefer diff hunks over final-code excerpts when old behavior is necessary to understand risk and the hunk contains both removals and additions
- prefer final-code excerpts for addition-only changes, even when the added code is semantically important
- prefer final-code excerpts over noisy hunks when the post-change code carries the review point clearly
- prefer a representative hunk over hiding meaningful before/after behavior entirely
- do not skip formatting or cosmetic changes by default; only collapse them when they are purely mechanical and the exact result does not help review
- show small cosmetic changes when they affect visible text, layout, docs, tests, naming, or readability
- do not use GitHub API file reads for review content once PR refs can be fetched locally
- explain code in simple terms; avoid unexplained jargon, clever phrasing, or assumptions that the reader knows the codebase
- avoid speculative, low-confidence, or low-value findings
- if a section is mostly noise, say so briefly and compress it
- optimize for signal, pacing, and usefulness over completeness theater
- run the false-positive check for every section that has concerns; do not skip it for speed

End each section by explicitly waiting for the user, for example:
`Questions on this section, or should I move to the next one?`
