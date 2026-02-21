
## Recipes

### Sample directory structure

- `.github`
  - [`dependabot.yml`](#dependabotyml)
  - [`workflows`](https://docs.github.com/en/actions)
    - [`test.yml`](#run-gradle-test)
    - [`release-publish.yml`](#release-application-build-to-github-and-publish)

### dependabot.yml

[Docs](https://docs.github.com/en/code-security/dependabot/dependabot-version-updates/configuration-options-for-the-dependabot.yml-file)

```yaml
version: 2
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

### Run gradle test

```yaml
name: Test

on:
  push:
    branches:
      - main
      - master
    paths-ignore:
      - "*.md"
  pull_request:
    branches:
      - main
      - master
    paths-ignore:
      - "*.md"

jobs:
  test:
    uses: itzg/github-workflows/.github/workflows/gradle-build.yml@main
    with: 
      arguments: test
      include-test-report: true
```

If NodeJS support is needed, add
```yaml
  include-node-js: true
```
and adjust `npm-cache-dependency-path`, if needed.

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
      GITHUB_PUBLISH_TOKEN: ${{ secrets.PUSH_ACCESS_GITHUB_TOKEN }}
```

If NodeJS support is needed, add
```yaml
  include-node-js: true
```
and adjust `npm-cache-dependency-path`, if needed.

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

### Simple Boot Image to GHCR

```yaml
name: "Build image"
on:
  push:
    branches:
      - main
    tags:
      - "[0-9]+.[0-9]+.[0-9]+"

jobs:
  build:
    uses: itzg/github-workflows/.github/workflows/simple-boot-image-to-ghcr.yml@main
    with:
      image-repo: "ghcr.io/itzg"
      image-platforms: "linux/amd64,linux/arm64"
      extra-gradle-tasks: test
```

If NodeJS support is needed, add
```yaml
  include-node-js: true
```
and adjust `npm-cache-dependency-path`, if needed.

### Go test with optional goreleaser

```yaml
    uses: itzg/github-workflows/.github/workflows/go-test.yml@main
```

### Go release with goreleaser including image

```yaml
    uses: itzg/github-workflows/.github/workflows/go-with-releaser-image.yml@main
    secrets:
      image-registry-username: ${{ secrets.DOCKERHUB_USERNAME }}
      image-registry-password: ${{ secrets.DOCKERHUB_TOKEN }}
      # optional
      scoop-tap-github-token: ${{ secrets.SCOOP_BUCKET_GITHUB_TOKEN }}
```

**NOTE** The `repository.token` in `.goreleaser.yaml` needs to be configured as

```yaml
      token: "{{ .Env.SCOOP_TAP_GITHUB_TOKEN }}"
```

### Go release with goreleaser

```yaml
    uses: itzg/github-workflows/.github/workflows/go-with-releaser.yml@main
```

**NOTE** The `repository.token` in `.goreleaser.yaml` needs to be configured as

```yaml
      token: "{{ .Env.SCOOP_TAP_GITHUB_TOKEN }}"
```

### Discord issues notifier

Secrets to declare in `https://github.com/.../settings/secrets/actions`:

- `DISCORD_WEBHOOK`

```yaml
name: Issues notify

on:
  issues:
    types:
      - opened
      - labeled
      - unlabeled
      - closed

jobs:
  notify:
    uses: itzg/github-workflows/.github/workflows/issues-notify-discord.yml@main
    secrets:
      discordWebhook: "${{secrets.DISCORD_WEBHOOK}}"
```