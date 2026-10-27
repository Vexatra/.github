# Contributing

Thank you for your interest. Please read this before opening an issue or a pull request.

## Product repositories are private

Vexatra's products are developed in private repositories by a small team working with AI coding agents. They take no outside code. Customers report problems through the product's support channel.

## Public repositories

SDKs, the design system and other public repositories welcome issues.

Bug reports are the most useful contribution. Search the existing issues first, then open one with the bug template: what you did, what you expected, what happened, and the version and platform. A minimal reproduction makes a fix much more likely.

A feature request describes the problem you want solved rather than a specific implementation; a maintainer turns it into planned work when it is agreed.

Report security vulnerabilities privately, as described in [SECURITY.md](SECURITY.md), never in a public issue.

## Pull requests need an approved issue

Review capacity is limited, so unsolicited pull requests are not accepted.

1. Open an issue, or comment on an existing one, describing the change you would like to make.
2. Wait until a maintainer labels it `accepted` and agrees on the scope.
3. Then open a pull request that links the issue with `Closes #<number>`.

Pull requests without an accepted issue are closed with a link to this file.

## What a pull request needs

- One problem per pull request.
- A title in the conventional commit form `type(scope): summary`, for example `fix(api): refuse an expired webhook timestamp`. The repository's `AGENTS.md` lists its scopes.
- The description template filled in: the problem, the change, and how you verified it.
- Passing checks. Each repository's `docs/operations/` explains how to run them locally.
- Tests that follow the repository's rules: they check behavior, fail without your change, and run fast. Purely visual changes are shown with before and after captures instead.

If you use AI tools, follow the [AI policy](AI_POLICY.md).

## License

By contributing you agree that your contribution is licensed under the license of the repository you contribute to.
