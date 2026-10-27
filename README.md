# Vexatra organization defaults

Shared files for every repository of the Vexatra organization.

- `profile/README.md` is the organization's GitHub profile.
- `CONTRIBUTING.md`, `SECURITY.md` and `AI_POLICY.md`, the issue templates and the pull request template apply to every repository that has none of its own.
- `docs/repository-standard.md` is the standard every repository follows: layout, documentation, code, tests, work, changes, security and releases.
- Reusable workflows each repository calls from its own CI, with their usage in the comment at the top of each file:
  - `pr-title.yml`: the conventional pull request title and a body release-please can parse.
  - `changes.yml`: tells CI whether a pull request changes anything beyond documentation.
  - `zizmor.yml`: audits the repository's workflows.
  - `pr-gate.yml`: closes a pull request from outside the organization that does not close an `accepted` issue. Public repositories only.
- `.github/actions/docs-check/` is the documentation check as an action. A repository calls it from its own job after checking itself out; the action runs `scripts/docs_check.py` from the same commit. Run the script locally from a repository root with `python3 <path>/scripts/docs_check.py`.
- Every action is pinned by commit SHA, with the version in a comment; Dependabot keeps the pins current.
- Releases: when a reusable workflow, the action or a template changes, the manager tags a release (`v0.0.1`, `v0.0.2`, ...). Repositories pin them by a release's commit with its tag in a comment (`@<sha> # v0.0.1`).
