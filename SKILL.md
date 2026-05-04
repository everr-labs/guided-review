---
name: guided-review
description: Use when the user asks to review or understand a GitHub PR, local branch, diff, or code changes interactively, one section at a time, with pauses for questions, navigation, or PR comment publishing.
---

# Guided Review

## Purpose

Turn a code review into a guided conversation. Help the user understand the change, inspect the important code, ask questions, and optionally publish PR comments as the review moves section by section.

Use simple, beginner-friendly language. Explain project terms when they matter. Do not assume the user already knows the codebase.

## Triggers

Use this skill for prompts like:
- "Walk me through this PR"
- "Review this branch with me"
- "Can we go section by section?"
- "Help me understand this diff"
- "Let's review and leave comments as we go"

Do not use this skill when the user asks for a normal one-shot code review, a short summary, or direct implementation work unless they also ask for an interactive walkthrough.

## Non-Negotiables

1. Show one review section at a time, then stop and wait.
2. Start with a short section map and ask whether to skip or reorder anything before showing the first section.
3. Keep important code visible. Do not replace code review with a prose-only summary.
4. Group sections by intent, not filenames alone.
5. Down-rank noise, but do not hide small changes that affect visible text, layout, docs, tests, naming, or readability.
6. Mention only plausible bugs or improvements that are useful to call out.
7. Label each concern and uncovered scenario with `high`, `medium`, or `low`.
8. Run a false-positive check before presenting any concern.
9. Use local Git for review content once PR refs can be fetched locally.

## Workflow

1. **Get the review source.**
   - For a GitHub PR, fetch the PR locally and inspect it with Git.
   - For a local branch or diff, identify the base and head refs before building sections.

2. **Build a section map.**
   Good section boundaries include API shape, data flow, behavior changes, migrations, tests, cleanup, or user-facing changes.

3. **Compress noise carefully.**
   Safe-to-collapse examples include generated output, broad mechanical renames, import ordering, formatting-only churn, and repeated edits where one example explains the pattern.

4. **Present the map and pause.**
   Ask whether the user wants to skip or reorder any section. Wait for their answer before opening the first section.

5. **Review one section at a time.**
   Use the section template below. After each section, stop and wait for the user.

## Section Template

For each section, include:

- **What we are reviewing:** short explanation plus the files in this section
- **Code:** enough diff hunks or final-code excerpts for real review
- **What changed:** plain-language explanation of behavior or risk
- **Concerns:** useful findings only, each labeled `high`, `medium`, or `low`; say when there are no concerns
- **Test coverage:** what appears covered
- **Uncovered scenarios:** business logic that is not clearly tested, each labeled `high`, `medium`, or `low`; say when nothing important is missing
- **Pause:** end with a direct wait question, such as `Questions on this section, or should I move to the next one?`

Prefer diff hunks when old behavior is needed to understand the risk. Prefer final-code excerpts for addition-only changes or when the post-change code shows the point more clearly. Use a representative hunk when a repeated pattern matters.

## False-Positive Check

Before presenting a concern, verify:

- The concern follows from the shown code, not a guess.
- The surrounding code does not already handle the case.
- The impact is worth the user's attention.
- The severity label matches the actual risk.

If confidence is low, ask a question instead of stating a finding.

## Comment Publishing

When the user asks to publish a PR comment:

1. **Draft the comment.** Make it concise, specific, and grounded in the current section. Inline comments should state the concrete code concern or question directly. Avoid labels or meta-commentary such as "Inline question about test coverage."
2. **Check for similar existing comments.** Fetch existing inline and top-level PR comments. Skip duplicates that already cover the same file, function, line range, or observation.
3. **Preview before publishing.** Show every visible thing that will be posted: comment body, file and line range or top-level location, and any review summary required by the tool. Wait for explicit user approval.
4. **Publish or report why not.** Use the best available GitHub capability. If skipping due to similarity, summarize the existing comment so the user can decide whether to post anyway.
5. **Stay in place.** After publishing, remain in the same section unless the user asks to move.

## Interaction Rules

While paused, the user may ask questions, request more or less detail, publish a PR comment, move to the next section, or jump back to an earlier section.

Do not advance automatically. Keep the pace controlled by the user.

## Example Shape

User: "Walk me through PR 42 section by section."

Assistant:
1. Fetches PR refs locally.
2. Builds a map such as `API changes`, `Validation flow`, `Tests`.
3. Shows the map and asks what to skip or reorder.
4. After approval, presents only `API changes` with files, code, explanation, concerns, test coverage, and uncovered scenarios.
5. Stops with: `Questions on this section, or should I move to the next one?`
