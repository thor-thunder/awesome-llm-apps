---
name: code-writing
description: Write, edit, refactor, or debug code in any project. The moment code is involved, activate the Context7 MCP (resolve-library-id → get-library-docs) to ground the work in the library/framework's current docs, and verify correctness (~0.90 confidence) before committing to an answer. Use this skill whenever the user asks you to write code, add a feature, fix a bug, refactor, scaffold, or work inside a codebase — even if they never say the word "code" — and especially when the work touches a third-party library, framework, or API (e.g. Next.js, pandas, Stripe, Google ADK) whose current behavior matters.
---

# Code writing and editing

The point of this skill is to avoid two common failure modes: writing code against
half-remembered (and often outdated) API knowledge, and declaring something "done" before it
actually runs. You fix both by pulling current docs first and verifying before you commit to an
answer.

## Stay interactive

Treat the work as a short collaboration, not a one-shot dump:

   • Confirm the goal before you start — restate what you're building and the success criteria in
     a sentence, so a wrong assumption gets caught early instead of after 200 lines.
   • Decompose the task into small, verifiable steps and tackle them one at a time. Smaller steps
     are easier to check and cheaper to redo.
   • Show your reasoning at decision points (why this library, why this approach) so the user can
     redirect you.
   • Check in before any big move — a new dependency, a schema change, a wide refactor — rather
     than presenting it as a fait accompli.

## Ground the work in current docs (Context7 first)

   • Always activate the Context7 MCP when dealing with projects and code. Pull the current docs
     for the specific library, framework, or API you're about to use *before* you write against
     it. Library APIs drift between versions; the docs for the version actually in the project
     beat your training-time recollection.

     Context7 works in two steps 📚: first `resolve-library-id` turns a library name (e.g.
     "next.js", "pandas") into its Context7-compatible ID, then `get-library-docs` fetches that
     library's up-to-date docs — optionally scoped to a topic so you pull only the relevant pages.

     **Example:** about to add a Next.js API route? First query Context7 for the latest Next.js
     routing docs (App Router vs Pages Router differ), then write the handler.

     **Example:** editing a Python script that calls the Stripe SDK? Pull Context7's Stripe docs
     for the version pinned in the project before touching the call — argument names and idioms
     change across major versions.

   • If Context7 doesn't cover the library, or its docs look stale or contradictory, fall back to
     a targeted WebFetch / web search for a known-good, current example — but only when needed.

   • Working on Google's stack? Reach for **Google ADK** (the numbered `google_adk_crash_course`
     tutorial in this repo is the reference pattern) and set `GOOGLE_API_KEY` for Gemini / ADK models.

## Verify before you commit to an answer

Write code that is actually working and executable, not plausible-looking. Before you present or
write a solution, sanity-check it against the docs you pulled (and run it if you can), and keep
verifying until you're at least **0.90 confident** it's correct. The 0.90 bar is a prompt to
yourself: if you're below it, you haven't checked enough — read another doc page, run the code,
or test the edge case rather than shipping a guess.

Concretely, that usually means:

   • Cross-check every external API call against the docs (signature, required args, return shape).
   • Run or type-check the code where the environment allows, and read the error rather than
     guessing at a fix.
   • Walk the obvious edge cases (empty input, error path, off-by-one) before declaring success.

If you genuinely can't reach that confidence — the docs are ambiguous, you can't run it — say so
plainly and flag what you're unsure about, instead of presenting a guess as a finished answer.
