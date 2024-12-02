<p align="center">
  <h1 align="center">Snyker</h1>
</p>
<p align="center">
An opinionated CLI wrapper around <a href="https://snyk.io/">Snyk</a> for purging vulnerabilities from Node projects
</p>
<p align="center">
   <a href="https://github.com/asos/snyker/tags/"><img src="https://img.shields.io/github/tag/asos/snyker" alt="Current version" /></a>
   <img src="https://github.com/asos/snyker/workflows/Test/badge.svg" alt="Current test status" />
   <a href="http://makeapullrequest.com"><img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg" alt="PRs are welcome" /></a>
   <a href="https://github.com/asos/snyker/issues/"><img src="https://img.shields.io/github/issues/asos/snyker" alt="snyker issues" /></a>
   <img src="https://img.shields.io/github/stars/asos/snyker" alt="snyker stars" />
   <img src="https://img.shields.io/github/forks/asos/snyker" alt="snyker forks" />
   <img src="https://img.shields.io/github/license/asos/snyker" alt="snyker license" />
   <a href="https://github.com/asos/snyker/graphs/commit-activity"><img src="https://img.shields.io/badge/Maintained%3F-yes-green.svg" alt="snyker is maintained" /></a>
</p>
<p align="center">
  <i>You're not you when you've got hundreds of vulnerable paths</i>
</p>

---

## Getting Started

```bash
# Start fixing vulnerabilities straight away using NPX
npx @asos/snyker

# Add to your global NPM packages
npm i -g @asos/snyker

# Or to your global Yarn packages
yarn global add @asos/snyker
```

## About

The Snyk CLI is great for reporting vulnerabilities and providing top level dependency upgrades and patches, but struggles when the vulnerability rests within a nested "transitive" sub-dependency. This is despite the fact that many sub-dependencies have reasonable flexibility in the version ranges they allow for their own dependencies.

This CLI takes a brute-force approach to solving this limitation of Snyk. It purges the `.snyk` file from a project, checks for vulnerable paths using Snyk, then forces `yarn` / `npm` to try to upgrade any dependency along the vulnerable paths before finally ignoring any vulnerability that cannot be fixed in the previous steps. If a patch is available for any outstanding vulnerability then it is also added to the Snyk policy.

Note that this tool obeys your defined package version ranges and therefore can't fix anything that requires a major upgrade if you are only permitting minor or patch upgrades.

This tool also does not make use of Snyk's ability to perform package major upgrades. It will simply ignore vulnerabilities that cannot be fixed in the aforementioned steps. _It is on you to sanity check anything that this tool decides to ignore._

Snyker will list the known vulnerabilities it has been unable to fix. If Snyk reports that there are major upgrades available to fix one or more of the outstanding vulnerabilities, Snyker will output a recommended `yarn` / `npm` command for performing the upgrade(s).

It is recommended that you use this tool alongside the official Snyk CLI, not replace it completely.

## Usage

### Options

```console
snyker --retries 3 --lockfile package-lock.json --preserve-integrity
```

| Flag                   | Description                                                              | Default     |
| ---------------------- | -------------------------------------------------------------------------| ----------- |
| `--lockfile <string>`  | Specify the lockfile to use (e.g. `yarn.lock` or `package-lock.json`).   | Attempts to find a `yarn.lock` or `package-lock.json` then defaults to `yarn.lock` |
| `--retries <int>`      | Will set the number of times to retry logical steps of Snyker.           | `2`         |
| `--preserve-integrity` | Will not attempt to update integrity hash when `sha1` is used. \*        | `false`     |

> \* It is highly recommended to use `sha512` for the integrity hash algorithm which is default for `npm`. However, when using private repositories such as Azure Artifacts, they do not support anything other than `sha1`. In turn, if the integrity is removed, the subsequent `npm install` command does not re-instate these. This flag is a workaround for this issue.

## Alternatives

### Snyk Pull Requests

[Snyk supports a pull or merge request integration](https://docs.snyk.io/scan-using-snyk/pull-requests/snyk-fix-pull-or-merge-requests) for your source control repositories which can upgrade your dependencies based on scan results.

This behaves similar to Snyker in providing a capability to upgrade dependencies, but is not available as a CLI and does not bundle ignore behaviours at the same time.

### Snyk Ignore

The Snyk CLI supports a [`snyk ignore` command](https://github.com/snyk/cli/blob/main/help/cli-commands/ignore.md) to ignore a stated issue according to its snyk ID for all occurrences, its expiry date, a reason, or according to paths in the filesystem.

This commands does not perform any dependency upgrades and requires you to manually look up the vulnerability's ID to execute the correct ignore command.

Snyker currently includes the `snyk ignore` capability as part of it's process.

### Snyk Protect

Snyk supports a separate [`@snyk/protect`](https://github.com/snyk/cli/tree/main/packages/snyk-protect#readme) CLI, replacing the older `snyk protect` command for patching vulnerable dependencies.

The Snyker maintainers generally advise against the usage of closed source patches for your dependencies.

### Snyk Fix

Snyk has released a [closed beta](https://docs.snyk.io/getting-started/snyk-release-process#closed-beta) [`snyk fix` command](https://docs.snyk.io/snyk-cli/scan-and-maintain-projects-using-the-cli/automatic-fixing-with-snyk-fix) that aims to automatically apply the recommended updates, but this is currently only available for Enterprise customers using Python.

### Snyk Wizard

Snyk used to support a `snyk wizard` command which would perform dependency upgrades and policy ignores [but this was removed on 31 March 2022](https://updates.snyk.io/snyk-wizard-and-snyk-protect-removal-224137).

## Contributing

Please check out the [CONTRIBUTING](./docs/CONTRIBUTING.md) docs.

## Changelog

Please check out the [CHANGELOG](./docs/CHANGELOG.md) docs.

---

## License

Snyker is licensed under the [MIT License](./LICENSE).
