---
name: finish-task
description: Close out a SocialOS task - verify, review, document, commit and open a PR.
disable-model-invocation: true
---

Finish the current task. Issue number (if given): $ARGUMENTS

1. Run `dotnet build -warnaserror`, `dotnet test` and `dotnet format --verify-no-changes`. If `web/` changed, also run `npm run lint`, `npm test` and `npm run build` in `web/`. Show each command and its result. Stop and fix failures before continuing.
2. Use the `reviewer` subagent to review the branch diff. Fix findings that affect correctness, security or requirements. List any finding you chose not to fix and why.
3. Check whether docs need updating: ADRs, `docs/architecture/`, README, `CLAUDE.md` commands. Update only what this change made inaccurate.
4. Make sure commits follow Conventional Commits and each one builds.
5. Draft a PR description with: what changed, why, how it was verified (commands and results), and anything left out of scope. Show it to me before running `gh pr create`.
