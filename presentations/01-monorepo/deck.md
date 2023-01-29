---
marp: true
theme: dracula
transition: fade
# backgroundColor: #000
# backgroundImage: url('https://marp.app/assets/hero-background.svg')
---

<style>
  section {
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
</style>

# <!-- fit --> fubotv-web

🔗 https://github.com/fubotv/fubotv-web

---

## Topics to cover

- Prerequisites
- Tools used
- How to get started

---

## Prerequisites

- git
- nvm
- node v16 **(minimum)**
  - run `nvm use`
- [pnpm](https://pnpm.io)
  - [why?](https://pnpm.io/motivation)
  - [installation](https://pnpm.io/installation)

---

## <!-- fit --> Getting Started

---

## Tools used 🛠️

- [typescript](https://www.typescriptlang.org/)
- [turborepo](https://turbo.build/repo)
- [changesets](https://github.com/changesets/changesets)

---

## Config 🟰 Pkg

- The repository has a separate package for our Typescript config and eslint config. They get processed in the build process and are published to npm by the Github Action provided by `changesets` under the `@fubotv` scope.
  - [tsconfig](https://github.com/fubotv/fubotv-web/tree/dev/packages/tsconfig)
  - [eslint-config](https://github.com/fubotv/fubotv-web/tree/dev/packages/eslint-config)

---

## About this repo 📁

```sh
apps/
├── sandbox               # playground for POCs
└── showcase              # storyboook app to showcase our design-system
packages/
├── components            # TS port of shared-components
├── cookies
├── dates                 # light wrapper around date-fns
├── ds-web                # 🆕 design system
├── eslint-config         # base eslint config
├── flowgen               # fork of flowgen npm pkg
├── js-client             # TS port of api-client
├── routing
├── tsconfig              # TS configs other workspaces can extend
├── types                 # Typescript types that need to be distributed
└── utils                 # common JS/TS utils
```

---

## Commands 📝

### First run

To ensure the proper commands run on commit, such as `prettier` and `eslint`, run:

```sh
pnpm dlx husky install
```

---

### Install dependencies and build workspaces

```sh
pnpm install && pnpm build
```

---

### Adding or remove packages

You can add/remove packages normally from within a workspace, but anytime you are in the root of the repository you will need to use pnpm's [filtering](https://pnpm.io/filtering) feature to target the workspace.

```sh
pnpm add <package> --filter <workspace>
pnpm remove <package> --filter <workspace>

# example
pnpm add date-fns --filter js-client
```

---

### All workspaces

Run commands across all workspaces. This uses [`turbo run <command>`](https://turborepo.org/docs/reference/command-line-reference#turbo-run-task).

| Command          | Runs                       |
| ---------------- | -------------------------- |
| `pnpm changeset` | Adds a new changeset       |
| `pnpm lint`      | Lints all workspaces       |
| `pnpm test`      | Tests all workspaces       |
| `pnpm clean`     | Remove generated files     |
| `pnpm format`    | Format files with prettier |

---

## Turborepo ⚡️

> "A high-performance build system for JavaScript and TypeScript codebases"

[Why?](https://turbo.build/repo/docs/core-concepts/monorepos)

---

### How do we use it?

In the root of the monorepo, a `turbo.json` file houses the [config](https://turbo.build/repo/docs/reference/configuration) to make turborepo aware of our tasks/scripts across the various workspaces.

### Caching Tasks

Turborepo's [caching](https://turbo.build/repo/docs/core-concepts/caching) feature helps us in our build pipeline by avoiding doing any unnecessary work.

---

### Filtering Workspaces

By default, running `pnpm dev` at the root will execute the dev task in **all available workspaces**. To avoid this, we can utilize turborepo's [filter](https://turbo.build/repo/docs/core-concepts/monorepos/filtering) flag to select the workspaces we want to execute our task in.

#### Example

```sh
# working on packages/js-client
pnpm dev --filter="js-client..."
```

---

## Changesets 🦋

We have chosen [changesets](https://github.com/changesets/changesets) as the tool that will manage updating package dependencies and versioning. It also takes care of publishing the packages to npm.

---

### Adding a changeset

A changeset describes changes made in a branch or commit. It holds three bits of information:

- What packages we need to release
- The type of version bump for each changed package, respectivelu
- A changelog entry for the released packages

---

Add a changeset if you have made any changes that will require a package version bump and release:

1.  Run `pnpm changeset`
2.  Select the packages you want to include using the ↑ and ↓ keys to navigate to packages, and space to select one. Hit enter when all desired packages are selected.
3.  Select a [bump type](https://semver.org/spec/v2.0.0.html)`(major|minor|patch)` for each selected package.
4.  Provide a message to be written into the changelog on the next release.

---

### Out of scope for the changelog

Generally, changes related to these topics can be omitted:

- Dev dependencies upgrades
- Chores (infrastructure, release process…)

---

### Github Actions

We have set up some Github actions to automate the process of releasing that take advantage of actions written by the open source community.

- [Snapshot](https://github.com/fubotv/fubotv-web/actions/workflows/changesets-snapshot.yml) makes use of [seek-oss/changesets-snapshot](https://github.com/seek-oss/changesets-snapshot)
- [Release](https://github.com/fubotv/fubotv-web/actions/workflows/changesets-release.yml) makes use of [changesets/action](https://github.com/changesets/action)

The snapshot action allows you to prepublish one or more of the packages in the monorepo to npm. This can be done by using the link above and using the `Run workflow ▼` dropdown to select your branch and clicking the `Run workflow` button.

---

### Live Demo

![auto](https://media.giphy.com/media/elrFAUtV7ZOH7TSPhF/giphy-downsized-large.gif)
