# AGENTS.md

This file provides guidance to AI coding agents when working with code in this repository.

## Status: Empty — not yet started

This repo currently contains only a `LICENSE` and a stub `README.md`. There is no SDK code yet.

## Intent

A Python client SDK for the Insignia Education API (`/api/v1`), mirroring the structure and
conventions of [`insignia-education/api-sdk-js`](../api-sdk-js) — the mature reference
implementation for this API:

- One module/class per API resource (`Courses`, `Users`, `Auth`, …), matching HTTP verbs
  (`get`, `post`, `put`, `patch`, `delete`).
- Versioned client: a `v1` client that is frozen once the API's v1 is declared stable; a future
  `v2` client added alongside it, never replacing it.
- Zero/minimal runtime dependencies where the ecosystem allows it (`requests` or stdlib `urllib`
  are reasonable exceptions — JS's zero-dependency constraint doesn't map 1:1 to Python).
- No hardcoded human-readable strings — errors expose a machine-readable status/body; translation
  is the consuming app's job.
- Every method verified with a real integration test against a running `api` instance, not ad-hoc
  scripts.

## When code is added here

Once this repo has real source, replace this file (and `CLAUDE.md`) with full documentation
following the pattern in `api-sdk-js/AGENTS.md`: requirements, quick start, structure, usage
example, conventions, "Adding a new resource" steps, testing coverage requirements, the API↔SDK
sync rule, and a "Never do" list. Also add `.ai/guidelines/`, `.claude/agents/`, and
`.claude/skills/` at that point — there's nothing to tailor them to yet.

## API ↔ SDK sync rule (applies once code exists)

Any endpoint added, renamed, or removed in [`insignia-education/api`](../api) must be reflected
here in the same task, exactly as required for `api-sdk-js`.


---

## Working Style

- **Think before coding.** State your assumptions out loud. If the request is ambiguous, ask. If a simpler approach exists, push back. Stop when confused — name what is unclear; do not pick one interpretation and run.
- **Simplicity first.** Write the minimum code that solves the problem. No speculative abstractions. No flexibility nobody asked for. The test: would a senior engineer call this overcomplicated?
- **Surgical changes.** Touch only what the task requires. Do not improve neighboring code. Do not refactor what is not broken. Every changed line must trace back to the request.
- **Goal-driven execution.** Turn vague instructions into verifiable targets before writing a line. "Add validation" becomes "write tests for invalid inputs, then make them pass."
## Git

- **NEVER commit in the agent's or Claude's name.** All commits must be authored solely by the human developer. Do not add `Co-Authored-By` trailers that name Claude or any AI agent — in shared/collaborative repositories this would falsely attribute work and obscure accountability.
- **Committing and pushing is fine.** You may commit and push as part of normal work — no need to ask first each time. The only hard rule is authorship: commits sail under whatever author name/email git is already configured with (the human developer's), never an agent's identity.

## Git workflow

- **Always confirm the branch first.** Before starting any task, check the current branch and ask the user which branch to use — don't assume, even when one looks obviously right. A stale branch, or another session's branch mid-task, can look plausible and still be wrong.
- **Branch per task, cut from `master` only.**
  ```
  git checkout master && git pull && git checkout -b task/<name>
  ```
- **Never commit directly to `master`.** All work happens on a task branch. (This repo has no `beta` branch — it's a published package, not a deployed app; ignore any stale reference elsewhere in this file to one.)
- **Keep local `master` updated.** `git pull` it before cutting a new branch and before merging any PR into it.
- **One branch at a time.** If multiple sessions are working on different things here concurrently, don't spin up a branch per session — consolidate onto a single branch and tell the user that's what's happening.
- **Always sync before committing.** Merge `master` into your task branch before every commit — the branch should never drift from `master`.
- **Merging to `master` needs explicit permission.** Never merge a branch into `master` on your own judgment — open a PR (`gh pr create`) and ask the user before merging it. Merges to `master` go through GitHub, not a local `git merge`.
- **After a branch's PR merges, clean up.** Delete it locally and on GitHub, and switch back to `master` locally.
  ```
  git checkout master && git pull
  git branch -d task/<name>
  git push origin --delete task/<name>
  ```


## Before starting a task

See "Git workflow" above — check the current branch and ask the user which one to use before doing anything else.

## Communication style
- TL;DR always. Fewest words possible. No preamble, no step-by-step narration, no "here is what I did" summaries, no explaining what you are about to do.
- Log every command executed and every file write, verbatim — syscalls and writes, not model narration.
- Report outputs, not steps: state what a command produced/changed, not the fact that you ran it or why.

---

## Git safety (CRITICAL — read every session)

**DO NOT MESS WITH GIT.** DO NOT run `git checkout`, `git stash`, `git reset`, `git restore`,
`git clean`, or any command that discards or overwrites working-tree changes. These repos often
carry large amounts of **uncommitted** work, and these commands will destroy it irreversibly.

If you need to change the current branch: **commit the work first, or ask the user to commit.**
Never revert, discard, or overwrite changes via git without explicit permission from the user.

## NEVER TOUCH `insignia-education/infra/envs`

**Read-only. Never create, edit, move, or delete anything under
`insignia-education/infra/envs/` — not one line, for any reason.**

That directory is the owner's personal record of the deployed environments,
kept manually on their machine. It is gitignored, so there is no history and
**nothing there can be recovered from git.** A prod env file was already lost
once this way.

- Need to know what a deployed env contains? Read it, don't write it.
- An env var needs to change? Say so and let the owner make the edit.
- Recovering a lost env: the deploy pipeline stores the authoritative copy in
  AWS SSM Parameter Store (e.g. `/ie/api/env-prod`), and the EC2 host holds a
  `chmod 600` copy at the deploy's `ENV_FILE_PATH`. Restore from SSM, and hand
  the file to the owner rather than writing into `envs/` yourself.
