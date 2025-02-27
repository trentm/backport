# Config file options

`backport` reads options from two configuration files:

- [Global config](#global-config-backportconfigjson)
- [Project config](#project-config-backportrcjson)

All file config options be overriden via CLI options.

## Global config (`.backport/config.json`)

During installation `backport` will create an empty configuration file in `~/.backport/config.json`. You must update this file with a [Github Access Token](https://github.com/settings/tokens)

Example:

```json
{
  "accessToken": "very-secret-token"
}
```

#### `accessToken` **required**

Personal access token.
Access tokens can be created here: https://github.com/settings/tokens

Please select the necessary access scopes:

**For public and private repos (recommended)**
![image](https://user-images.githubusercontent.com/209966/67081197-fe93d380-f196-11e9-8891-c6ba8c4686a4.png)

<img width="971" alt="image" src="https://user-images.githubusercontent.com/7416358/226398066-54cd918e-7d5a-420b-9f84-bb34f9f43dd6.png">


**For public repos only**
![image](https://user-images.githubusercontent.com/209966/67081207-018ec400-f197-11e9-86aa-4ae4a003fcbd.png)

<img width="971" alt="image" src="https://user-images.githubusercontent.com/7416358/226398088-715a5bab-7ac8-4733-b48c-d94da593ca04.png">


#### `editor`

If a merge conflicts occurs during backportin your editor of choice will be opened to make it easier for you to resolve the conflict

```json
{
  "editor": "code"
}
```

## Project config (`.backportrc.json`)

A `.backportrc.json` config file should be added to the root of each project where `backport` is used. This is useful for sharing configuration options with other project contributors.

Example:

```json
{
  "repoOwner": "elastic",
  "repoName": "kibana",
  "targetBranchChoices": ["6.x", "6.3", "6.2", "6.1", "6.0"],
  "targetPRLabels": ["backport"]
}
```

#### `repoName` **required**

Name of repository

```json
{
  "repoName": "kibana"
}
```

#### `repoOwner` **required**

Owner of repository (Github organization or Github username)

```json
{
  "repoOwner": "elastic"
}
```

#### `targetBranchChoices` **required**

List of target branches the user can select interactively.

```json
{
  "targetBranchChoices": ["6.3", "6.2", "6.1", "6.0"]
}
```

The array can contain branch names as strings or objects that also contains the field `checked` which indicates whether the branch should be pre-selected. It is useful to pre-select branches you often backport to.

```json
{
  "targetBranchChoices": [
    { "name": "6.3", "checked": true },
    "6.2",
    "6.1",
    "6.0"
  ]
}
```

## General configuration options

The following options can be used in both the global config, project config, and passed in through CLI.

#### `assignees`

Add assignees to the target pull request

```json
{
  "assignees": ["sqren"]
}
```

#### `author`

By default only commits from the authenticated user are displayed. To see commits from another user use `--author john.doe`. To see commits from any user use `--all` (cli-only flag).

```json
{
  "author": "sqren"
}
```

To view commits form all users (equivalent to `backport --all`):

```json
{
  "author": null
}
```

#### `autoAssign`

Automatically add the current user as assignee to the target pull request.
Default: `false`

```json
{
  "autoAssign": true
}
```

#### `autoMerge`

Automatically merge the backport pull request when `true`.

Default: `false`

```json
{
  "autoMerge": true
}
```

#### `autoMergeMethod`

When `autoMerge: true` the backport pull request will be merged with one of the following methods: `merge`, `rebase`, `squash`.

Default: `merge`

```json
{
  "autoMergeMethod": "squash"
}
```

#### `backportBinary`

The official way to run backport is by installing it globally and running `backport`. There are however many other ways to run it. It can be installed locally and then run with `npx backport`/`yarn backport` or wrapped in a custom script and run like a node.js application `node scripts/backport`.

If you are using a non-standard way to run backport it is useful to add that here, to ensure that the Github comments posted by the backport tool includes the correct command.

Important: Since this command will be included in status comments on Github, it must NOT include sensitive information, like an access token.

Example of how `backportBinary` is used:

<img src="https://user-images.githubusercontent.com/209966/148454343-0a0ee0d1-cc3f-4c45-aa58-f3a5c28b81be.png" width="600"/>

Default: `backport`

```json
{
  "backportBinary": "node scripts/backport"
}
```

#### `branchLabelMapping`

Automatically detech which branches a pull request should be backported to, based on the pull request labels.

```json
{
  "branchLabelMapping": {
    "^v7.8.0$": "7.x",
    "^v(\\d+).(\\d+).\\d+$": "$1.$2"
  }
}
```

_Note: backslashes must be escaped._

#### `dir`

Clone repository into custom directory

Default: `~/.backport/repositories/`

```json
{
  "dir": "/my/custom/tmp/repo/location"
}
```

#### `dateSince`

Only display commits newer than the specified date

#### `dateUntil`

Only display commits older than the specified date

#### `fork`

`true`: Create backport branch in users own fork

`false`: Create backport branch in the origin repository

Default: `true`

```json
{
  "fork": false
}
```

#### `gitHostname`

Hostname for Github.

Default: `github.com`

```json
{
  "gitHostname": "github.my-private-company.com"
}
```

#### `githubApiBaseUrlV3`

Base url for Github's REST (v3) API

Default: `https://api.github.com`

```json
{
  "githubApiBaseUrlV3": "https://api.github.my-private-company.com"
}
```

#### `githubApiBaseUrlV4`

Base url for Github's GraphQL (v4) API

Default: `https://api.github.com/graphql`

```json
{
  "githubApiBaseUrlV4": "https://github-enterprise.acme-inc.com/api"
}
```

#### `mainline`

When backporting a merge commit the parent id must be specified. This is directly passed to `git cherry-pick` and additional details can be read on the [Git Docs](https://git-scm.com/docs/git-cherry-pick#Documentation/git-cherry-pick.txt---mainlineparent-number)

**Examples:**

- Defaults to 1 when no parent id is given: `backport --mainline`
- Specifying parent id: `backport --mainline 2`

#### maxNumber

Number of commits that will be listed for the user to choose from.

Default: 10

```json
{
  "maxNumber": 20
}
```

#### `multipleCommits`

`true`: you will be able to select multiple commits to backport. You will use `<space>` to select, and `<enter>` to confirm you selection.

`false`: you will only be able to select a single commit. You will use `<enter>` to confirm the selected item.

Default: `false`

#### `multipleBranches`

`true`: you will be able to select multiple branches to backport to. You will use `<space>` to select, and <enter> to confirm you selection.

`false`: you will only be able to select a single branch. You will use `<enter>` to confirm the selected item.

Default: `true`

#### commitPaths

Only list commits touching files under the specified path

```json
{
  "commitPaths": ["my/folder"]
}
```

#### `prTitle`

Title for the target pull request
Template values:

- `{targetBranch}`: Branch the backport PR will be targeting
- `{commitMessages}`: Message of backported commit. For multiple commits the messages will be separated by pipes (`|`).

Default: `"[{targetBranch}] {commitMessages}"`

```json
{
  "prTitle": "{commitMessages} backport for {targetBranch}"
}
```

#### `prDescription`

Title for the target pull request
Template values:

- `{targetBranch}`: Branch the backport PR will be targeting
- `{commitMessages}`: Message of backported commit. For multiple commits the messages will be separated by pipes (`|`).
- `{defaultPrDescription}`: The default PR description. Using this makes it easy to append and prepend text to the existing description

```json
{
  "prDescription": "Backports the following commits to {targetBranch}:\n{commitMessages}"
}
```

**Example**
For people who often want to append the same text, they can create a bash alias:

```sh
alias backport-skip-ci='backport --pr-description "{defaultPrDescription} [skip-ci]"'
```

#### `prFilter`

Filter source pull requests by any [Github query](https://help.github.com/en/github/searching-for-information-on-github/understanding-the-search-syntax). Text with whitespace [must contain escaped quotes](https://help.github.com/en/github/searching-for-information-on-github/understanding-the-search-syntax#use-quotation-marks-for-queries-with-whitespace).

```json
{
  "prFilter": "label: \"Backport Needed\""
}
```

#### `publishStatusCommentOnAbort`

Publish a status comment to the source pull request if the backport was aborted

Default: `False`

```json
{
  "publishStatusCommentOnSuccess": false
}
```

#### `publishStatusCommentOnSuccess`

Publish a status comment to the source pull request if all backports succeeded

Default: `true`

```json
{
  "publishStatusCommentOnSuccess": false
}
```

#### `publishStatusCommentOnFailure`

Publish a status comment to the source pull request if some backports failed

Default: `false`

```json
{
  "publishStatusCommentOnFailure": false
}
```

#### `pullNumber`

Backport a pull request by specifying its number

#### `resetAuthor`

Change the author of the backported commit to the current user

#### `reviewers`

Add reviewers to the target pull request

#### `sha`

Backport a commit by specifying its commit sha

#### `signoff`

Pass the --signoff option to the cherry-pick command

#### `sourceBranch`

By default the list of commits will be sourced from the repository's default branch (mostly "master"). Use `sourceBranch` to list and backport commits from other branches than the default.

Default: master (unless the default branch on Github is changed)

```json
{
  "sourceBranch": "7.x"
}
```

#### `sourcePRLabels`

Labels that will be added to the source (original) pull request. This can be useful if you, at a later time, want to find the PRs that were already backported.

```json
{
  "sourcePRLabels": ["was-backported"]
}
```

#### `targetBranches`

Overrides `targetBranchChoices` so instead of displaying a prompt with target branches to choose from, the selected commit(s) will be backported directly to the branches defined in `targetBranches`

```json
{
  "targetBranches": ["7.x", "7.7"]
}
```

#### `targetPRLabels`

Labels that will be added to the target (backport) pull request. This can be useful if you, at a later time, want to find the backport PRs.

```json
{
  "targetPRLabels": ["backport", "apm-team"]
}
```
