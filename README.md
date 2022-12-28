# Release npm package using conventional commits

Release npm package to Github and the npm registry using conventional commits.

The action performs the following steps:

1) Check out the code
2) Figure out what type of release is do, if any
3) Generate a CHANGELOG
4) Bump the version, commit the changes, tag and push to Github
5) Create a Github release using the tag and the changelog information
6) Build the package
7) Publish to the npm package registry

## Inputs

* **Required** ``github-token``: Personal access token for GitHub with the ``public_repo`` scope.
* **Required** ``npm-token``: Granular access token for npmjs.org with read and write permissions for the package to release.

## Usage

### Personal GitHub repository

1) Generate the GitHub auth token and store it as a new repository secret ``RELEASE_PAT``
2) Generate the npm auth token and store it as new repository secret ``NPM_TOKEN``
3) Create ``.github/workflows/release.yml`` with the contents below

```yaml
name: Release
on: workflow_dispatch

jobs:
  npm-registry:
    name: Npm registry
    permissions:
      contents: write
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          github-token: ${{ secrets.RELEASE_PAT }}
          npm-token: ${{ secrets.NPM_TOKEN }}
```

Releases are triggered manually or on a cron schedule. Adjust `on` for the trigger.

### @nextcloud GitHub repository

Repositories in the [@nextcloud](https://github.com/nextcloud) organization have to use environments to protect repository secrets.

1) Create a GitHub environment "npm release"
   1) Enable reviewers and pick the maintainers of the repository
   2) Generate the GitHub auth token and store it as a new environment secret ``RELEASE_PAT``
   3) Generate the npm auth token and store it as new environment secret ``NPM_TOKEN``
2) Create ``.github/workflows/release.yml`` with the contents below

```yaml
name: Release
on: workflow_dispatch

jobs:
  npm-registry:
    name: Npm registry
    permissions:
      contents: write
    environment: npm registry
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          github-token: ${{ secrets.RELEASE_PAT }}
          npm-token: ${{ secrets.NPM_TOKEN }}
```

Releases are triggered manually and need an approval from the maintainers.
