# Repository standard

How every repository of the Vexatra organization is laid out, documented, tested and changed. The model is [pingdotgg/t3code](https://github.com/pingdotgg/t3code) and the Rawframe Project's repository standard; the rules below come from the research listed under [Sources](#sources).

## Contents

- [Principles](#principles)
- [Files](#files)
- [AGENTS.md](#agentsmd)
- [Documentation](#documentation)
- [Skills](#skills)
- [Code](#code)
- [Tests](#tests)
- [Work](#work)
- [Sessions](#sessions)
- [Changes](#changes)
- [Continuous integration](#continuous-integration)
- [Security and supply chain](#security-and-supply-chain)
- [Releases](#releases)
- [Presentation](#presentation)
- [Contributions and AI](#contributions-and-ai)
- [Enforcement](#enforcement)
- [Sources](#sources)

## Principles

1. **A repository reads like a product, not a diary.** It says what the product is and how it works today. History lives in git, pull requests and the changelog.
2. **Less context is better context.** Agents and people both do worse with long, stale instructions. Every line of guidance must prevent a real mistake.
3. **Rules that must always hold are enforced by a tool.** Prose is advice; a CI check, a lint rule or a hook is a guarantee.
4. **Code is the source of truth.** Documentation explains what the code cannot: intent, constraints and traps.
5. **Tests guard behavior, not structure.** A test that breaks when the implementation is refactored without a behavior change is a defect.
6. **The smallest model that makes the correct behavior unsurprising.** No machinery because it looks architecturally impressive; no complexity kept because it already exists.

## Files

A repository contains these documentation files and no others. The documentation check fails on any other Markdown file outside `docs/`, `.agents/` and `.github/`, apart from third-party files under a vendor folder.

| Path | Purpose |
|---|---|
| `README.md` | What the product is, why it exists, a quick start, and links. |
| `AGENTS.md` | The guide for coding agents. See [AGENTS.md](#agentsmd). |
| `CLAUDE.md` | One line: `@AGENTS.md`. |
| `CHANGELOG.md` | User-facing changes per release. See [Releases](#releases). |
| `LICENSE` | The license. |
| `docs/README.md` | Index of the documentation. |
| `docs/user/` | How to use the product. |
| `docs/internals/` | How it works and why. |
| `docs/operations/` | How to develop, test and release it. |
| `.agents/skills/` | Agent skills. `.claude/skills` is a symlink to it. |
| `.github/` | Workflows, and templates only where the organization defaults do not fit. |

`CONTRIBUTING.md`, `SECURITY.md`, `AI_POLICY.md` and the issue and pull request templates come from the organization's `.github` repository. A repository adds its own copy only to change it.

Never committed: plans, specifications separate from `docs/internals/`, research notes, work logs, state files, decision journals, task lists, session notes, benchmark scratch, screenshots for a single pull request. Working material stays outside the repository; progress lives in the issue.

## AGENTS.md

Hand-written, at most 200 lines. For each line ask whether an agent would make a mistake without it; if not, delete it.

It contains, in this order:

1. **What the product is** in two or three sentences, and the few things it never compromises on.
2. **Commands** an agent cannot guess: build, the changed-files check, the full check, how to run one test.
3. **Ways to hurt yourself:** the actions in this repository that damage state, other sessions or the machine, and what to do instead. Every repository includes: never kill a process found by matching a name or path; kill only a PID you started.
4. **Invariants** that are not visible from one file (dependency direction, money and time rules, tenant isolation, generated files).
5. **Testing rules** specific to the repository, on top of [Tests](#tests).
6. **Documentation rules:** a link to this standard's [Documentation](#documentation) section and anything specific.
7. **Pull requests:** the commit title scopes the repository uses.

It does not contain directory tours, file catalogs, feature lists, API descriptions, history, dates, decision numbers, or anything the code already says. Long procedures that are needed only sometimes become skills.

## Documentation

### Structure

- `docs/user/` follows the reader's task: install, use, configure, troubleshoot. One page per major feature: what it does, how to start, what is not obvious. No descriptions of every button, field or state.
- `docs/internals/` holds one living page per topic. Each page explains how the part works, why it is built that way, the constraints that span components, and the traps that are hard to see in the source. It links to source instead of copying it. `overview.md` and `glossary.md` are always present. Before the code exists, these pages are the design the code is held to, written in the same voice.
- `docs/operations/` holds the maintainer's procedures: development setup, checks, release, debugging.
- API reference is generated (OpenAPI, rustdoc, TypeDoc); it is not written by hand. Generated documentation, including an `llms.txt` digest, is built into the published site, not committed.

### Rules

- Most code changes need no documentation change. Before adding a paragraph, ask what a maintainer or user would get wrong without it.
- When a documented decision or behavior changes, rewrite or delete the affected text in the same pull request. Never append an amendment, a dated note or a second account. Git keeps the history.
- A new page needs a distinct and lasting reason to exist. Prefer extending an existing page.
- Write in the product's voice, present tense, third person. No first person, no "we decided on", no dates, no names, no internal numbers (`D123`, `ADR-0012`). The same holds for code comments: a comment explains why, it never cites a record.
- A local implementation detail goes in a code comment next to the code. Comments say why, never what.
- Pages stay under 400 lines; pages over 100 lines start with a table of contents.

### Decisions

There is no numbered decision log. A decision is recorded by writing its reasoning into the relevant `docs/internals/` page, under the part it governs, and by the pull request that made it. Alternatives that were considered and rejected are kept in one short paragraph only when a future maintainer would otherwise try them again.

## Skills

- Skills live in `.agents/skills/<name>/SKILL.md`, with `.claude/skills` a symlink to `.agents/skills`.
- A skill is written only after the same need has come up more than once. Before writing it, write at least three example tasks it must handle; check a fresh session handles them better with the skill than without.
- `name` is lowercase with hyphens, preferably a gerund (`releasing-a-version`). `description` is third person and says what the skill does and when to use it.
- The body stays under 500 lines. Deterministic steps are scripts the skill runs, not prose. No dates or time-sensitive statements.
- A skill that more than one repository needs is kept in the organization's `.github` repository and synced into each repository.

## Code

### All languages

- Everything written to files is English: code, identifiers, comments, docs, commit messages.
- No em dash character in any file, commit, issue or pull request.
- Comments explain why: an invariant, a constraint from outside the code, a trap. A comment that restates the code is deleted. No commented-out code, no `TODO` without an issue number on the same line.
- Names say what a thing is in the product's vocabulary (`docs/internals/glossary.md`). No abbreviations a newcomer must guess, no `utils`, `helpers`, `common` or `misc` modules; code lives with the concept it serves.
- One owner per concept: one module, one name, one public path. No globals, singletons or service locators; what a part needs is handed to it.
- Handwritten source files are reported from 600 lines and fail the check at 1,000; split along an ownership seam.
- Generated files carry a header naming their generator and are never edited by hand. `AGENTS.md` lists them.

### Rust

- Formatting is `rustfmt` with the repository's `rustfmt.toml`; `cargo fmt --check` gates.
- `clippy` runs with `-D warnings` and the workspace lint table in `Cargo.toml` (`[workspace.lints]`): `unwrap_used`, `expect_used`, `panic`, `todo`, `dbg_macro`, `print_stdout` and `print_stderr` are denied outside tests and binaries' `main`; `unsafe_code` is forbidden unless a crate states why at its root.
- Errors: libraries define typed errors with `thiserror`; `anyhow` is allowed only in binaries and tools. Untrusted input is validated and refused with an error, never with a panic or an assertion.
- `debug_assert!` states internal invariants; `assert!` stays only where continuing would corrupt data.
- Time comes from an injected `Clock`; randomness from an injected source. `SystemTime::now`, `Instant::now` and `thread::sleep` are disallowed by `clippy.toml` outside the adapters that implement them.
- Every crate has `#![doc]` explaining its role in two sentences; public items carry rustdoc where the name is not enough.

### TypeScript

- `strict` TypeScript with `noUncheckedIndexedAccess` and `exactOptionalPropertyTypes`; no `any` without a comment saying why.
- Formatting and linting are one toolchain, configured once at the root; warnings fail the check. `knip` reports unused files, exports and dependencies.
- Package manager is pnpm with a committed lockfile and exact versions; Node is pinned in `package.json` `engines` and `.node-version`.
- Shared contracts (API types) are generated, never hand-copied.

## Tests

### What gets a test

Tests protect the core of the product: money and tax results, state machines, wire and file formats, handling of hostile or malformed input, tenant isolation, the public API contract, and the bug classes that have already escaped once.

- **Logic, data, state and protocol bugs:** the fix comes with the fewest tests that fail on the unfixed code for the right reason, added to the existing test file of that module.
- **Visual and cosmetic changes** (spacing, color, alignment, wording, menu entries): no test. The proof is a before and after capture in the pull request.
- **New features:** tests cover the behavior a user or caller relies on, not every input. Add a case only when the code branches differently for it.
- **No test** asserts the current implementation instead of the specification: call order, private state, internal sizes, mocks wired to mirror the code. Components are not rendered to markup to assert props or callback wiring.
- **Property tests** state laws of the domain (a prorated amount never exceeds the full price; an invoice total equals the sum of its rounded lines plus its taxes) and are preferred over long tables of examples.
- **Golden values** that pin a promised output (an invoice's totals for a handful of tax scenarios, an import's result for a fixture database) are part of the specification, not change detectors. Keep them few.

### How a test is written

- It checks observable behavior through the public interface and survives refactoring.
- Every assertion can fail. Prefer the strongest exact check over a weak one.
- It is deterministic and hermetic: no wall clock, no sleeps, no polling, no external network (localhost is fine), no retries. A flaky test is fixed, not retried; a skipped test names its tracking issue on the line above (`// quarantine: #123 why`).
- It is fast: a unit test runs in milliseconds, an integration test in about a second. A slow test is a defect.
- Database tests run against real PostgreSQL, each in its own database cloned from a migrated template, never against mocks of the store.
- External services (control panels, registrars, payment gateways, e-invoice integrators) are tested against local fakes that speak their protocol, built from recorded and anonymized exchanges. No test reaches a real third-party service.
- When it fails, the message says what broke. Test output stays short; details go to a log file.
- **End-to-end tests** drive a real browser against a real server and database. They carry an `e2e` label, stay out of the changed-files check, and are kept to a few journeys a user takes that module tests cannot reach.
- Size and speed budgets the product promises are checked with a stated tolerance, never as exact counts.

### Fuzzing

Code that reads untrusted or complex input is fuzzed: importers, email parsing, template rendering, webhook payload parsing, license files. Pull requests that touch fuzzed code run a short pass; a nightly run fuzzes longer and grows the corpus. Every crash becomes a regression test. Mutation testing is not part of the checks.

### Budgets

- The changed-files check that agents run before pushing: under 2 minutes.
- The pull request check in CI: under 10 minutes.
- The full check on `main` and nightly: under 20 minutes.
- An extended nightly run (long fuzzing, load tests, every browser) may take up to two hours. It is not a required check.

Over budget, tests are pruned or merged before anything else is added.

## Work

- Work is tracked in GitHub issues. A session opens an issue for every piece of work it starts and closes it through the pull request that finishes it.
- The organization has one GitHub Project board, [Vexatra](https://github.com/orgs/Vexatra/projects/1). Columns: Backlog, Ready, In progress, In review, Done. Milestones group issues toward a release.
- The owner and the manager set milestones. Product sessions split them into issues and order Ready.
- A session takes the top issue of its repository's Ready column, assigns itself, moves it to In progress, and works on that one issue only.
- The issue body is the progress record: a short problem statement and a checklist the session updates. Findings that matter later go into the issue as comments, not into files.
- Labels: `bug`, `feature`, `docs`, `test`, `performance`, `security`, `chore`, `blocked`, `accepted`, `duplicate`, `wontfix`. `tools/repo-setup.sh` in the workspace applies them with the merge settings and the ruleset.

## Sessions

The workspace is `/home/vexatra`, a checkout of `vexatra/vexatra-org`. Each repository has one agent session, started by the owner and named after the repository. The manager session runs at the workspace root; the reviewer session reviews pull requests read-only. No session spawns subagents or parallel agents.

- A session works when it has work: an issue in its Ready column, a message from the manager, or a review on its pull request. With none of these it stops and waits; it does not poll on a short timer.
- A session that waits on something slow checks back at the pace of that thing.
- Heavy local jobs (full checks, large builds, fuzzing, load tests) run through the workspace's `tools/heavy.sh`, so sessions do not starve each other on the shared machine.

## Changes

- Changes reach `main` only through pull requests, with the required checks passing, linear history and no force pushes. Public repositories enforce this with a ruleset. Private repositories cannot on the organization's plan, so it is enforced locally: `tools/hooks/pre-push` refuses a push to `main`, sessions never push to `main` or merge, and the manager merges with `gh pr merge --squash` once `required` is green and the reviewer is satisfied.
- Branch names are short and hyphenated, without prefixes (`invoice-numbering`).
- Pull request titles are conventional commits: `type(scope): summary` in plain, lower-case language, under 72 characters. Types: `feat`, `fix`, `perf`, `refactor`, `docs`, `test`, `build`, `ci`, `chore`. Scopes are the repository's areas, listed in its `AGENTS.md`.
- The body follows the template: Problem, Change, Verification (what was run and what it showed). It ends with `Closes #<issue>`.
- Pull requests are squash-merged; the title becomes the commit on `main`. Auto-merge is on where the plan allows it.
- One issue, one pull request. Unrelated fixes go in separate pull requests.
- Before a pull request merges, the reviewer session reviews it in a fresh context: correctness first, then this standard.
- A red `main` is fixed before any other work.
- Commits and pull requests carry no `Co-Authored-By`, "Generated with", model or session lines; the owner is the author.

## Continuous integration

A repository's `ci.yml` lists what to check; the organization's reusable workflows say how. CI and a local run use the same commands (`tools/check.sh <tier>`).

- **Changed files.** The first job decides what a pull request touches; a documentation-only change skips the builds.
- **Toolchains.** Rust is pinned in `rust-toolchain.toml`, Node and pnpm in `package.json`. Linux jobs run in the organization's CI image, `ghcr.io/vexatra/ci:<YYYY.MM.N>`, with nothing installed during the run.
- **Caches.** Cargo, sccache and the pnpm store are restored for every run and saved only from `main`.
- **One required check.** The last job, `required`, runs `if: always()`, needs every other job, and fails if any needed job failed or was cancelled; a job skipped by the changes job counts as passed. The ruleset requires only `required` and `title / title`.
- **Concurrency.** A new push to a pull request cancels its running CI. Runs on `main` are never cancelled.
- **New tests repeat.** On pull requests, tests added or changed by the pull request run three times, so a flaky test fails before it merges.
- **Generated artifacts are checked.** The OpenAPI document, the generated API client, the `sqlx` offline query data and the plugin interface bindings are regenerated in CI and must match what is committed.
- **Workflow hygiene.** Every workflow sets `permissions: {}` at the top and grants each job what it needs, sets `defaults.run.shell: bash`, checks out with `persist-credentials: false`, and passes event data to scripts only through `env`. `zizmor` audits every workflow on each pull request.
- **Empty checks fail.** A step that feeds files to a tool fails when the list is empty; a test run fails when it finds no tests.
- **Runners.** Public repositories use GitHub-hosted runners. Private repositories run their light jobs (title, changes, zizmor) on GitHub-hosted runners within the plan's free minutes, and every build, test and scan on the organization's self-hosted runner on the workspace server (labels `self-hosted`, `linux`, `vexatra`). The runner group admits private repositories only, so code from a fork never reaches it. Jobs on it run heavy commands through `tools/heavy.sh`.

## Security and supply chain

- Every action in a workflow is pinned by commit SHA with its version in a comment; Dependabot keeps pins and dependencies current (weekly, grouped).
- Rust dependencies pass `cargo-deny` (licenses, advisories, bans, sources) and `cargo-vet`; JavaScript dependencies pass `osv-scanner` and the license allowlist. The allowlist is one file shared by both.
- Static analysis runs on every pull request: CodeQL on public repositories, Semgrep on private ones. Secrets are scanned on every push: GitHub secret scanning with push protection on public repositories, gitleaks in CI on private ones.
- Workflows declare the least `permissions` they need; the default token is read-only.

## Releases

- Versions follow Semantic Versioning. Tags are `vX.Y.Z`. Versions stay below 1.0.0 while the product's public contracts still change; after 1.0.0 only minor and patch releases ship, so a change that would break 1.x is designed to be additive.
- A change that breaks a public contract (the HTTP API, the plugin interface, the theme objects, the data an update migrates) carries `!` in its title and a `BREAKING CHANGE:` footer naming what changed.
- release-please keeps a release pull request open from the conventional commits, using the organization's release GitHub App token. Before merging it, the session rewrites its changelog entry into user language, grouped as Added, Changed, Fixed, Removed, Security, and replaces the notes in the pull request body with the same entry.
- Every release carries its artifacts, a `SHA256SUMS` file, an SPDX SBOM and a build provenance attestation. Container images are signed with cosign.

## Presentation

- The GitHub repository has a one-line description, topics and, where one exists, a homepage.
- `README.md` opens with the name, one sentence, and badges for CI and the latest release. Then: what it is and why, a quick start that works when copied, links to the docs, and the license. No roadmap, no history, no internal process.

## Contributions and AI

- The organization develops with AI agents and says so in `AI_POLICY.md`.
- Private product repositories take no outside contributions. Public repositories (SDKs, the design system) take issues from anyone and pull requests only for an issue a maintainer labeled `accepted`; `pr-gate.yml` closes the rest.
- `AI_POLICY.md` asks outside contributors to disclose AI use and to understand every line they submit.

## Enforcement

The organization's `.github` repository provides reusable workflows that every repository calls:

- **Documentation check:** only the files listed under [Files](#files); no em dash; no internal record numbers or dated notes; size limits for `AGENTS.md`, pages and skills; links resolve; `CLAUDE.md` is exactly `@AGENTS.md`.
- **Pull request title check:** conventional commit format, a known scope, and a body that release-please parses cleanly.
- **Test hygiene:** lint rules that reject sleeps, wall-clock reads, polling loops, per-test timeouts, retries and unexplained skips in Rust and TypeScript tests.
- **Source rules:** file size limits, the module dependency graph, disallowed APIs.

## Sources

- Rawframe Project, repository standard: the base of this document.
- Anthropic, [Best practices for Claude Code](https://code.claude.com/docs/en/best-practices) and [Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices).
- Gloaguen et al., [Evaluating AGENTS.md](https://arxiv.org/abs/2602.11988): generated context files lower success and raise cost.
- [Rethinking the Value of Agent-Generated Tests](https://arxiv.org/html/2602.07900v2): more agent-written tests do not raise resolution rates.
- Kent Beck, [Test Desiderata](https://testdesiderata.com/).
- TigerBeetle, [TIGER_STYLE](https://github.com/tigerbeetle/tigerbeetle/blob/main/docs/TIGER_STYLE.md).
- Repositories read: [t3code](https://github.com/pingdotgg/t3code), [Ghostty](https://github.com/ghostty-org/ghostty), [uv](https://github.com/astral-sh/uv), [Zed](https://github.com/zed-industries/zed).
- [Diataxis](https://diataxis.fr/) for separating tutorials, how-to guides, reference and explanation.
