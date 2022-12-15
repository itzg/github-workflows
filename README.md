
## Recipes

### Run gradle test

```yaml
name: Test

on:
  push:
    branches:
      - master
    paths-ignore:
      - "*.md"
  pull_request:
    branches:
      - master
    paths-ignore:
      - "*.md"

jobs:
  test:
    uses: itzg/github-workflows/.github/workflows/gradle-build.yml@main
    with: 
      arguments: test
```

### Release application build to GitHub and publish

Uses [io.github.itzg.github-releaser](https://plugins.gradle.org/plugin/io.github.itzg.github-releaser) plugin

```yaml
name: Release and publish

on:
  push:
    tags:
      - "[0-9]+.[0-9]+.[0-9]+"
      - "[0-9]+.[0-9]+.[0-9]+-*"
  workflow_dispatch:

jobs:
  publish:
    uses: itzg/github-workflows/.github/workflows/gradle-build.yml@main
    with:
      arguments: >
        test 
        githubPublishApplication
      scoop-bucket-repo: itzg/scoop-bucket
      homebrew-tap-repo: itzg/homebrew-tap
    secrets:
      GITHUB_PUBLISH_TOKEN: ${{ secrets.GITHUB_PUBLISH_TOKEN }}
```

### Release gradle plugin

```yaml
name: Publish

on:
  push:
    tags:
      - "[0-9]+.[0-9]+.[0-9]+"

jobs:
  publish:
    uses: itzg/github-workflows/.github/workflows/publish-gradle-plugin.yml@main
    secrets:
      plugin-portal-key: ${{ secrets.PLUGIN_PORTAL_KEY }}
      plugin-portal-secret: ${{ secrets.PLUGIN_PORTAL_SECRET }}
```