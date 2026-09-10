# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Read `AGENTS.md` first — this repo is currently empty (just `LICENSE` + a stub `README.md`), and
`AGENTS.md` explains the intent (a Python mirror of `api-sdk-js`) and what to do once real code
lands here. Nothing Claude-specific to add until then.

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
