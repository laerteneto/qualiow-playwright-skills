# Contributing

Thanks for helping. This project is two things: Markdown references under `skills/` that an AI
assistant reads, and a small TypeScript CLI under `src/` that installs them. Most contributions
touch the Markdown.

## Setup

Requires Node.js >= 20.

```bash
git clone https://github.com/<you>/qualiow-playwright-skills.git
cd qualiow-playwright-skills
npm ci
npm run dev -- init --platforms claude --dry-run   # run the CLI from source
```

## Before opening a pull request

Run the same checks CI runs:

```bash
npm run lint            # type check
npm test                # node:test suite
npm run check:fences    # type-check every TypeScript example in skills/
npm run build
```

To see what your change generates, run the CLI against a scratch project and validate it:

```bash
mkdir -p /tmp/scratch && cd /tmp/scratch && npm init -y > /dev/null
npx tsx <repo>/bin/init.ts init --platforms claude,cursor,copilot,agents --packs core,templates,workflows --yes
npx tsx <repo>/scripts/validate-output.ts .
npx tsx <repo>/scripts/check-rule-drift.ts .
```

## Editing `skills/`

- **New references are picked up automatically.** A new `.md` file in `skills/core/` or
  `skills/templates/` is installed and linked from `skills/indexes/skill.md`.
- **TypeScript examples must compile.** `npm run check:fences` compiles every `ts`/`typescript`
  fence against the installed `@playwright/test`. An example that is wrong on purpose, or that is
  only part of a test or class, takes a directive comment on the line above the fence, such as
  `<!-- ts-check: skip -->`, `<!-- ts-check: wrap-test -->` or `<!-- ts-check: declare loginPage -->`.
  The full list is at the top of `scripts/check-ts-fences.ts`.
- **Shell commands need a grant.** Every command in a `bash` fence must be a shell builtin or be
  covered by the skill's `allowed-tools`. Don't start a command line with `VAR=value`.
- **Some rules live in more than one place.** `skills/rules.manifest.tsv` lists them. If you change
  one in `skills/core/conventions.md`, update the summaries in `skills/indexes/cursor-rules.mdc`
  and `skills/indexes/copilot-instructions.md` too, or the drift check fails.
- **Workflows are written once.** Edit the body in `skills/workflows/`; each platform's command file
  is a generated wrapper that points at it.
- **Say which Playwright version a feature needs** when it is newer than the oldest version the
  references support, e.g. "Playwright 1.63 and later".

## Editing `src/`

- Each module has a matching test in `tests/`; add or extend one with your change.
- Anything that writes or removes files must stay inside the project directory. Check each path
  with `assertInsideProject` from `src/containment.ts` before touching it.

## Commits and pull requests

- Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/) with an
  optional scope: `feat(skills): ...`, `fix(cli): ...`, `test: ...`, `docs: ...`, `ci: ...`,
  `chore(deps): ...`.
- Keep one change per pull request, and say in the description how you checked it.
- Leave the version number alone. Release commits set it.
- For a larger change, such as a new reference or workflow, open an issue first so the approach can
  be agreed before you write it.

## License

By contributing you agree that your contributions are licensed under the MIT License.
