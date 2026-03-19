# skills

Shared skills for the team.

## Available skills

- semantic-commits: Create git commits using Conventional Commit semantics (feat/fix/chore/refactor/docs/test/build/ci/perf/revert/style).

## Notes

Each skill is packaged as a folder with a `SKILL.md` file and optional supporting files.


# skills

Shared Codex skills for the team.

This repository contains reusable custom skills that can be installed through the Codex Skill Installer, either from the Codex app or from an active Codex session in the terminal.

## Available skills

* `semantic-commits`: Create git commits using Conventional Commit semantics (`feat` / `fix` / `chore` / `refactor` / `docs` / `test` / `build` / `ci` / `perf` / `revert` / `style`).
* `gh-pr-from-jira-ticket`: Create and open GitHub pull requests from Jira tickets using `gh pr create`, defaulting the base branch to `dev` unless a base is explicitly provided.

## Skill structure

Each skill is packaged as a folder with a `SKILL.md` file and optional supporting files.

Example:

```txt
semantic-commits/
  SKILL.md
```

## How to install skills

Skills can be installed using the **Codex Skill Installer**.

You can use it in two ways:

### 1. From the Codex app

Inside the Codex app, open the command menu by typing `/` and look for the Skill Installer option.

From there, you can install:

* curated skills from the OpenAI skills repository
* custom skills from any repository

If you provide a custom repository and do **not** specify a skill name, Codex will scan the repository, detect the available skills, and ask which one you want to install.

### 2. Install Skills In Codex (Terminal)

Use these in the Codex prompt (not your shell prompt):

1. Install a curated skill from `openai/skills`:
   - `$skill-installer <skill-name>`
   - Example: `$skill-installer gh-address-comments`
2. Install a skill from a GitHub path (custom or experimental):
   - `$skill-installer https://github.com/<owner>/<repo>/tree/<ref>/<path-to-skill>`
   - Example (experimental): `$skill-installer https://github.com/openai/skills/tree/main/skills/.experimental/<skill-name>`
3. Restart Codex to pick up newly installed skills.

## Installing a custom skill from this repository

This repository currently contains a custom skill called `semantic-commits`.

When installing from this repository, ask Codex to install the skill from this repo and be explicit about the install scope.

Example request inside Codex:

```txt
Install the `semantic-commits` skill from this repository at the project level.
```

Or, if installing from another repository:

```txt
Install the `semantic-commits` skill from <repo-url> at the project level.
```

## Important: choose the install scope explicitly

When using the Skill Installer, be explicit about **where** the skill should be installed.

In practice, Codex may default to installing skills at the **personal/global** level. For team workflows, that is not always what we want.

### Personal / global install

Use this when the skill is generally useful across many repositories.

Examples:

* semantic commit creation
* release note generation

### Project-level install

Use this when the skill is specific to one repository, codebase, framework, or architecture.

Examples:

* how to create a new endpoint in a specific Next.js API project
* repository-specific testing workflows
* project-specific deployment or release conventions

For those cases, install the skill at the **project/repository level**.

Example request:

```txt
Install the `semantic-commits` skill from this repository into this project.
```

Or, if you want to be more explicit:

```txt
Install the `semantic-commits` skill from this repository at the project level, using this repository path as the target.
```

## Why install at project level?

Some skills should live only inside a specific repository because they encode conventions or workflows that do not apply elsewhere.

Even if you already have a skill installed globally, you may still want to install it again at the project level when:

* you want the project to explicitly own that skill
* you want teammates to use it in the same repo context
* you want the skill to be available as part of the repository workflow
* the skill is tightly coupled to that codebase

## Example test with `semantic-commits`

Once the skill is installed, test it with a change in a repository and ask Codex to use the skill.

Example prompt:

```txt
Use the `semantic-commits` skill to create a commit message for my current changes.
```

Expected result:

* Codex analyzes the current diff
* proposes a commit message using Conventional Commit semantics
* uses the most appropriate type such as `feat`, `fix`, `chore`, `refactor`, etc.

Example outputs:

```txt
feat(auth): add refresh token support
fix(api): handle null response in user profile endpoint
chore(ci): update pnpm cache configuration
```

## Notes

* OpenAI curated skills are available by default through the Skill Installer.
* Custom skills can be installed from other repositories.
* If no skill name is specified, Codex can scan the repository and ask which skill you want to install.
* For repository-specific skills, prefer installing them at the **project level** rather than globally.
