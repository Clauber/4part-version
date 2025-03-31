# Nonstandard Version

> A fork of [standard-version](https://github.com/clauber/nonstandard-version) that supports 4-part versioning (x.y.z.a) instead of the standard semver 3-part versioning.

A utility for versioning using extended semver (supporting 4-part versions) and CHANGELOG generation powered by [Conventional Commits](https://conventionalcommits.org).

## Key Features

- Supports 4-part versioning (x.y.z.a) where:

  - x: Major version (breaking changes)
  - y: Minor version (new features)
  - z: Patch version (bug fixes)
  - a: Revision/Build number

- All the features of standard-version including:
  - Automatic CHANGELOG generation
  - Git tag creation
  - Commit message convention enforcement
  - Flexible configuration

## Version Commands

The package supports the following version commands:

- `--release-as <major|minor|patch|revision>`: Specify which part of the version to bump
- `--release-version <version>`: Specify an exact version (e.g., 1.2.3.4)

Examples:

```bash
# Bump revision number (1.2.3.4 -> 1.2.3.5)
npx nonstandard-version --release-as revision

# Set specific version
npx nonstandard-version --release-version 1.2.3.4

# Create pre-release with revision
npx nonstandard-version --release-as revision --prerelease alpha
```

![ci](https://github.com/clauber/nonstandard-version/workflows/ci/badge.svg)
[![NPM version](https://img.shields.io/npm/v/nonstandard-version.svg)](https://www.npmjs.com/package/nonstandard-version)
[![codecov](https://codecov.io/gh/clauber/nonstandard-version/branch/master/graph/badge.svg?token=J7zMN7vTTd)](https://codecov.io/gh/clauber/nonstandard-version)
[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg)](https://conventionalcommits.org)
[![Community slack](http://devtoolscommunity.herokuapp.com/badge.svg)](http://devtoolscommunity.herokuapp.com)

_Having problems? Want to contribute? Join us on the [node-tooling community Slack](http://devtoolscommunity.herokuapp.com)_.

_How It Works:_

1. Follow the [Conventional Commits Specification](https://conventionalcommits.org) in your repository.
2. When you're ready to release, run `nonstandard-version`.

`nonstandard-version` will then do the following:

1. Retrieve the current version of your repository by looking at `packageFiles`[[1]](#bumpfiles-packagefiles-and-updaters), falling back to the last `git tag`.
2. `bump` the version in `bumpFiles`[[1]](#bumpfiles-packagefiles-and-updaters) based on your commits.
3. Generates a `changelog` based on your commits (uses [conventional-changelog](https://github.com/conventional-changelog/conventional-changelog) under the hood).
4. Creates a new `commit` including your `bumpFiles`[[1]](#bumpfiles-packagefiles-and-updaters) and updated CHANGELOG.
5. Creates a new `tag` with the new version number.

### `bumpFiles`, `packageFiles` and `updaters`

`nonstandard-version` uses a few key concepts for handling version bumping in your project.

- **`packageFiles`** – User-defined files where versions can be read from _and_ be "bumped".
  - Examples: `package.json`, `manifest.json`
  - In most cases (including the default), `packageFiles` are a subset of `bumpFiles`.
- **`bumpFiles`** – User-defined files where versions should be "bumped", but not explicitly read from.
  - Examples: `package-lock.json`, `npm-shrinkwrap.json`
- **`updaters`** – Simple modules used for reading `packageFiles` and writing to `bumpFiles`.

By default, `nonstandard-version` assumes you're working in a NodeJS based project... because of this, for the majority of projects you might never need to interact with these options.

That said, if you find your self asking [How can I use nonstandard-version for additional metadata files, languages or version files?](#can-i-use-nonstandard-version-for-additional-metadata-files-languages-or-version-files) – these configuration options will help!

## Installing `nonstandard-version`

### As a local `npm run` script

Install and add to `devDependencies`:

```bash
npm i --save-dev nonstandard-version
```

Add an [`npm run` script](https://docs.npmjs.com/cli/run-script) to your `package.json`:

```json
{
  "scripts": {
    "release": "nonstandard-version"
  }
}
```

Now you can use `npm run release` in place of `npm version`.

This has the benefit of making your repo/package more portable, so that other developers can cut releases without having to globally install `nonstandard-version` on their machine.

### As global `bin`

Install globally (add to your `PATH`):

```bash
npm i -g nonstandard-version
```

Now you can use `nonstandard-version` in place of `npm version`.

This has the benefit of allowing you to use `nonstandard-version` on any repo/package without adding a dev dependency to each one.

### Using `npx`

As of `npm@5.2.0`, `npx` is installed alongside `npm`. Using `npx` you can use `nonstandard-version` without having to keep a `package.json` file by running: `npx nonstandard-version`.

This method is especially useful when using `nonstandard-version` in non-JavaScript projects.

## Configuration

You can configure `nonstandard-version` either by:

1. Placing a `nonstandard-version` stanza in your `package.json` (assuming
   your project is JavaScript).
2. Creating a `.versionrc`, `.versionrc.json` or `.versionrc.js`.

- If you are using a `.versionrc.js` your default export must be a configuration object, or a function returning a configuration object.

Any of the command line parameters accepted by `nonstandard-version` can instead
be provided via configuration. Please refer to the [conventional-changelog-config-spec](https://github.com/conventional-changelog/conventional-changelog-config-spec/) for details on available configuration options.

### Customizing CHANGELOG Generation

By default (as of `6.0.0`), `nonstandard-version` uses the [conventionalcommits preset](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-conventionalcommits).

This preset:

- Adheres closely to the [conventionalcommits.org](https://www.conventionalcommits.org)
  specification.
- Is highly configurable, following the configuration specification
  [maintained here](https://github.com/conventional-changelog/conventional-changelog-config-spec).
  - _We've documented these config settings as a recommendation to other tooling makers._

There are a variety of dials and knobs you can turn related to CHANGELOG generation.

As an example, suppose you're using GitLab, rather than GitHub, you might modify the following variables:

- `commitUrlFormat`: the URL format of commit SHAs detected in commit messages.
- `compareUrlFormat`: the URL format used to compare two tags.
- `issueUrlFormat`: the URL format used to link to issues.

Making these URLs match GitLab's format, rather than GitHub's.

## CLI Usage

> **NOTE:** To pass nested configurations to the CLI without defining them in the `package.json` use dot notation as the parameters `e.g. --skip.changelog`.

### First Release

To generate your changelog for your first release, simply do:

```sh
# npm run script
npm run release -- --first-release
# global bin
nonstandard-version --first-release
# npx
npx nonstandard-version --first-release
```

This will tag a release **without bumping the version `bumpFiles`[1]()**.

When you are ready, push the git tag and `npm publish` your first release. \o/

### Cutting Releases

If you typically use `npm version` to cut a new release, do this instead:

```sh
# npm run script
npm run release
# or global bin
nonstandard-version
```

As long as your git commit messages are conventional and accurate, you no longer need to specify the semver type - and you get CHANGELOG generation for free! \o/

After you cut a release, you can push the new git tag and `npm publish` (or `npm publish --tag next`) when you're ready.

### Release as a Pre-Release

Use the flag `--prerelease` to generate pre-releases:

Suppose the last version of your code is `1.2.3.4`, and your code to be committed has patched changes. Run:

```bash
# npm run script
npm run release -- --prerelease
```

This will tag your version as: `1.2.3.4-0`.

If you want to name the pre-release, you specify the name via `--prerelease <name>`.

For example, suppose your pre-release should contain the `alpha` prefix:

```bash
# npm run script
npm run release -- --prerelease alpha
```

This will tag the version as: `1.2.3.4-alpha.0`

### Release as a Target Type Imperatively (`npm version`-like)

To forgo the automated version bump use `--release-as` with the argument `major`, `minor` or `patch`.

Suppose the last version of your code is `1.0.0`, you've only landed `fix:` commits, but
you would like your next release to be a `minor`. Simply run the following:

```bash
# npm run script
npm run release -- --release-as minor
# Or
npm run release -- --release-as 1.1.0
```

You will get version `1.1.0` rather than what would be the auto-generated version `1.0.1`.

> **NOTE:** you can combine `--release-as` and `--prerelease` to generate a release. This is useful when publishing experimental feature(s).

### Prevent Git Hooks

If you use git hooks, like pre-commit, to test your code before committing, you can prevent hooks from being verified during the commit step by passing the `--no-verify` option:

```sh
# npm run script
npm run release -- --no-verify
# or global bin
nonstandard-version --no-verify
```

### Signing Commits and Tags

If you have your GPG key set up, add the `--sign` or `-s` flag to your `nonstandard-version` command.

### Lifecycle Scripts

`nonstandard-version` supports lifecycle scripts. These allow you to execute your
own supplementary commands during the release. The following
hooks are available and execute in the order documented:

- `prerelease`: executed before anything happens. If the `prerelease` script returns a
  non-zero exit code, versioning will be aborted, but it has no other effect on the
  process.
- `prebump`/`postbump`: executed before and after the version is bumped. If the `prebump`
  script returns a version #, it will be used rather than
  the version calculated by `nonstandard-version`.
- `prechangelog`/`postchangelog`: executes before and after the CHANGELOG is generated.
- `precommit`/`postcommit`: called before and after the commit step.
- `pretag`/`posttag`: called before and after the tagging step.

Simply add the following to your package.json to configure lifecycle scripts:

```json
{
  "nonstandard-version": {
    "scripts": {
      "prebump": "echo 9.9.9"
    }
  }
}
```

As an example to change from using GitHub to track your items to using your projects Jira use a
`postchangelog` script to replace the url fragment containing 'https://github.com/`myproject`/issues/'
with a link to your Jira - assuming you have already installed [replace](https://www.npmjs.com/package/replace)

```json
{
  "nonstandard-version": {
    "scripts": {
      "postchangelog": "replace 'https://github.com/myproject/issues/' 'https://myjira/browse/' CHANGELOG.md"
    }
  }
}
```

### Skipping Lifecycle Steps

You can skip any of the lifecycle steps (`bump`, `changelog`, `commit`, `tag`),
by adding the following to your package.json:

```json
{
  "nonstandard-version": {
    "skip": {
      "changelog": true
    }
  }
}
```

### Committing Generated Artifacts in the Release Commit

If you want to commit generated artifacts in the release commit, you can use the `--commit-all` or `-a` flag. You will need to stage the artifacts you want to commit, so your `release` command could look like this:

```json
{
  "nonstandard-version": {
    "scripts": {
      "prerelease": "webpack -p --bail && git add <file(s) to commit>"
    }
  }
}
```

```json
{
  "scripts": {
    "release": "nonstandard-version -a"
  }
}
```

### Dry Run Mode

running `nonstandard-version` with the flag `--dry-run` allows you to see what
commands would be run, without committing to git or updating files.

```

```
