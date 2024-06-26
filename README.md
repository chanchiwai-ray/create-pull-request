# canonical/create-pull-request

This GitHub action automates the process of creating a pull request from local
changes. It utilizes the GitHub REST API to create commits. If the GitHub token
used in this action is a bot token, the commits will be created as signed and
shown as verified.

## Inputs

### `inputs.github-token`

The GitHub token required to perform actions such as pushing commits and
creating pull requests.

### `inputs.base`

The base branch to create the pull request against. Defaults to the current
`github.ref` context if not set. The git reference for the base must exists
locally (i.e. "fetched").


### `inputs.branch-name`

The name of the new branch to be created by this action. If an existing branch
with the same name exists in the repository, the action will fail, unless the
`upsert` option is enabled. In that case, the branch will be force-updated.

### `inputs.commit-message`

The commit message for the new commit created by this action.

### `inputs.title`

The title of the pull request that will be created.

### `inputs.body`

**Optional**

The body content of the pull request. This can provide additional information or
context for the changes being made.

### `inputs.repository`

**Optional**

The repository to create the branch and pull request on, for example
`canonical/create-pull-request`. Defaults to the current repository if not set.

### `inputs.upsert`

**Options:** `true | false`

**Default:** `false`

By default, the action fails if there's an existing branch with the same name in
the repository. Enabling `upsert` allows this action to update the existing
branch and its related pull request instead of doing nothing and failing.

**WARNING**: In upsert mode, commits in the existing branch will be
irreversibly removed.

### `inputs.ignore-no-changes`

**Options:** `true | false`

**Default:** `false`

By default, the action fails if no local changes are detected. Enabling this
option allows the action to do nothing and exit successfully when there are no
changes.

### `inputs.auto-merge`

**Options:** `true | false`

**Default:** `false`

When set to true, the action will enable auto-merge using the squash merge
method for the pull request and delete the branch after merge.

## Usage

### Creating pull requests in the current repository

Create a `new-branch` branch with the changes to the current repository.

```yaml
  create-pull-request:
    permissions: write-all
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Change local files
        run: |
          echo hello > hello
          echo new-test > test

      - name: Create pull request
        uses: canonical/create-pull-request@main
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          commit-message: Test commit message
          branch-name: new-branch
          title: Test pull request
          body: Test pull request body
```

### Creating pull requests in a different repository

Create a `new-branch` branch with the changes to a different repository (i.e.
`test-owner/test-repo` in this example). Note, that the `github-token` will need
to be a `repo` scoped [Personal Access Token (PAT)][1] or it can be a [github
app token][2] with access to that repository.

```yaml
  create-pull-request:
    permissions: write-all
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          repository: test-owner/test-repo

      - name: Change local files
        run: |
          echo hello > hello
          echo new-test > test

      - name: Create pull request
        uses: canonical/create-pull-request@main
        with:
          github-token: ${{ secrets.TOKEN_FOR_TEST_REPO }}
          commit-message: Test commit message
          branch-name: new-branch
          title: Test pull request
          body: Test pull request body
          repository: test-owner/test-repo
```

[1]: https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token
[2]: https://docs.github.com/en/apps/creating-github-apps/authenticating-with-a-github-app/generating-a-user-access-token-for-a-github-app
