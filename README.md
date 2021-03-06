# sentry-releases-action

[![license](https://img.shields.io/github/license/tclindner/sentry-releases-action.svg?maxAge=2592000&style=flat-square)](https://github.com/tclindner/sentry-releases-action/blob/master/LICENSE)
<a href="https://github.com/tclindner/sentry-releases-action"><img alt="GitHub Actions status" src="https://github.com/tclindner/sentry-releases-action/workflows/ci/badge.svg"></a>
[![Dependency Status](https://david-dm.org/tclindner/sentry-releases-action.svg?style=flat-square)](https://david-dm.org/tclindner/sentry-releases-action)
[![devDependency Status](https://david-dm.org/tclindner/sentry-releases-action/dev-status.svg?style=flat-square)](https://david-dm.org/tclindner/sentry-releases-action#info=devDependencies)


> A GitHub action that creates [releases for Sentry.io](https://docs.sentry.io/workflow/releases/?platform=javascript).

## What is sentry-releases-action?

A GitHub action that makes is easy to create a release in Sentry.io based on events in GitHub. Examples:

* a GitHub release is published
* a commit is pushed to master
* a pull request is merged to master

## How do I use it?

First thing first, let's make sure you have the necessary pre-requisites.

### Pre-requisites
Create a workflow `.yml` file in your repo's `.github/workflows` directory. An [example workflow](#example-workflow---create-a-release) is available below. For more information, reference the GitHub Help Documentation for [Creating a workflow file](https://help.github.com/en/articles/configuring-a-workflow#creating-a-workflow-file).

### Inputs

#### `tagName`

**Required** The tag being released. This is used as the Sentry release name. You can optionally prefix it using `releaseNamePrefix`.

#### `environment`

**Required** The name of the environment the release was deployed to.

#### `releaseNamePrefix`

**Optional** String that is prefixed to the tag to form the Sentry release name.

> Please review Sentry's documentation regarding max length and supported characters in release names.

For more information on these inputs, see the [API Documentation](https://developer.github.com/v3/repos/releases/#input)

### Environment Variables

#### `SENTRY_AUTH_TOKEN`

**Required** Sentry auth token.

#### `SENTRY_ORG`

**Required** Sentry organization.

#### `SENTRY_PROJECT`

**Required** Sentry project name.

#### `SENTRY_URL`

**Optional** URL to the Sentry instance, useful for e.g. on-prem deployments.

## Example usage

```yml
name: Create a Sentry.io release
uses: tclindner/sentry-releases-action@v1.0.0
env:
  SENTRY_AUTH_TOKEN: ${{ secrets.SENTRY_AUTH_TOKEN }}
  SENTRY_ORG: myAwesomeOrg
  SENTRY_PROJECT: myAwesomeProject
with:
  tagName: ${{ github.ref }}
  environment: qa
```

> Note: `sentry-releases-action` will automatically trim `refs/tags/` from `tagName`. This means you can pass `GITHUB_REF` directly from release events without the need of mutating it first.

### Full example workflow

On every GitHub `release` event.

```yaml
name: ReleaseWorkflow

on:
  release:
    types: [published, prereleased]


jobs:
  createSentryRelease:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@master
      - name: Create a Sentry.io release
        uses: tclindner/sentry-releases-action@v1.0.0
        env:
          SENTRY_AUTH_TOKEN: ${{ secrets.SENTRY_AUTH_TOKEN }}
          SENTRY_ORG: myAwesomeOrg
          SENTRY_PROJECT: myAwesomeProject
        with:
          tagName: ${{ github.ref }}
          environment: qa
```

Assume you tagged your release as `v1.1.0`. `github.ref` would equal `refs/tags/v1.1.0`. This action automatically strips `refs/tags/`, so the Sentry release name is `v1.1.0`.

### Example workflow with optional release prefix

On every GitHub `release` event.

```yaml
name: ReleaseWorkflow

on:
  release:
    types: [published, prereleased]


jobs:
  createSentryRelease:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@master
      - name: Create a Sentry.io release
        uses: tclindner/sentry-releases-action@v1.0.0
        env:
          SENTRY_AUTH_TOKEN: ${{ secrets.SENTRY_AUTH_TOKEN }}
          SENTRY_ORG: myAwesomeOrg
          SENTRY_PROJECT: myAwesomeProject
        with:
          tagName: ${{ github.ref }}
          environment: qa
          releaseNamePrefix: myAwesomeProject-
```

Scenario 1: Assume you tagged your release as `v1.1.0`. `github.ref` would equal `refs/tags/v1.1.0`. This action automatically strips `refs/tags/`, so the Sentry release name is `myAwesomeProject-v1.1.0`.

Scenario 2: Assume you tagged your release as `1.1.0` and you set `releaseNamePrefix` to `myAwesomeProject@`. `github.ref` would equal `refs/tags/1.1.0`. This action automatically strips `refs/tags/`, so the Sentry release name is `myAwesomeProject@1.1.0`.

> Note: This action only works on Linux x86_64 systems.

## Related

[sentry-release-deploy-action](https://github.com/tclindner/sentry-release-deploy-action) - Action used create a deploy for an existing release created by this action.

## Contributing

Please see [CONTRIBUTING.md](CONTRIBUTING.md).

## Release History

Please see [CHANGELOG.md](CHANGELOG.md).

## License

Copyright (c) 2019-2020 Thomas Lindner. Licensed under the MIT license.
