# dependabot-lein-runner

This action runs dependabot for Clojure (lein) projects using a [fork] of
[`dependabot/dependabot-core`] that has support for lein.

The action executes the `update.rb` script using a trimmed down version of the
`Dockerfile` from `dependabot-core` which contains only what is required to run
updates on `lein` projects. The image is pushed to
[`ghcr.io/carwow/dependabot-lein-runner`].

It uses the following variables:
- `token` the github access token to use to both check the private repositories and create pull requests (may be the GitHub Actions token or a personal access token)
- `registries` any github packages maven repository urls for private packages (comma separated, default `''`)
- `directory` the directory to look for `project.clj` within (default `'/'`)
- `repository` the repo to update in `owner/repo` format

Example daily workflow within for a single repo:

```yml
name: Daily Scheduled Bump
on:
  workflow_dispatch:
  schedule:
    - cron: '30 9 * * *'

jobs:
  update:
    runs-on: ubuntu-latest
    steps:
      - uses: carwow/dependabot-lein-runner@main
        with:
          token: ${{ github.token }}
          repository: ${{ github.repository }}
          directory: '/' # can be ommited
          registries: '' # can be ommited
```

Example workflow for multiple repos (must use a Personal Access Token in order to open PRs in repositories which are not the repository the action is running in):

```yml
name: Daily Scheduled Bump
on:
  workflow_dispatch:
  schedule:
    - cron: '30 9 * * *'

jobs:
  update:
    name: Update
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        projects:
          - repository: carwow/dependabot-lein-runner
            directory: '/dummy'

    steps:
      - uses: carwow/dependabot-lein-runner@main
        with:
          token: ${{ github.token }}
          repository: ${{ matrix.projects.repository }}
          directory: ${{ matrix.projects.directory }}
```


[`ghcr.io/carwow/dependabot-lein-runner`]: https://github.com/users/carwow/packages/container/package/dependabot-lein-runner
[`dependabot/dependabot-core`]: https://github.com/dependabot/dependabot-core
[fork]: https://github.com/carwow/dependabot-core/tree/leiningen
