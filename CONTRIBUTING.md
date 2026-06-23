# How this tap works and how to contribute to it

This document is for people adding a new tool to this tap or maintaining
an existing one. If you just want to install something, see the
[README](README.md).

## What is a Homebrew tap?

A [Homebrew tap](https://docs.brew.sh/Taps) is a Git repository that
Homebrew knows how to read. Running `brew tap westarete/tap` clones this
repository to your machine. After that, `brew install catalog` (or
`brew install westarete/tap/catalog`) finds the tool here.

Taps are how the Homebrew ecosystem distributes tools that aren't part
of the official
[homebrew-core](https://github.com/Homebrew/homebrew-core) or
[homebrew-cask](https://github.com/Homebrew/homebrew-cask) repositories.
Any public GitHub repository named `homebrew-<name>` is eligible to be
used as a tap with `brew tap <owner>/<name>`.

## What is a cask?

A [cask](https://docs.brew.sh/Cask-Language-Reference) is a small Ruby
file that tells Homebrew how to install a pre-built binary. Each cask
lives under `Casks/` and declares the download URL and SHA-256 checksum
for each platform variant, plus metadata like a description and
homepage.

Here is an annotated example from this tap (`Casks/catalog.rb`):

```ruby
cask "catalog" do
  version "0.1.1"

  on_macos do
    on_intel do
      sha256 "7ea901..."
      url "https://github.com/westarete/catalog/releases/download/v#{version}/catalog_darwin_amd64.tar.gz"
    end
    on_arm do
      sha256 "331e0d..."
      url "https://github.com/westarete/catalog/releases/download/v#{version}/catalog_darwin_arm64.tar.gz"
    end
  end

  on_linux do
    on_intel do
      sha256 "4c68f3..."
      url "https://github.com/westarete/catalog/releases/download/v#{version}/catalog_linux_amd64.tar.gz"
    end
    on_arm do
      sha256 "c5e027..."
      url "https://github.com/westarete/catalog/releases/download/v#{version}/catalog_linux_arm64.tar.gz"
    end
  end

  name "catalog"
  desc "Generates and maintains CATALOG.md — a behavior file that tells an AI agent when to pull each document into context."
  homepage "https://github.com/westarete/catalog"

  livecheck do
    skip "Auto-generated on release."
  end

  binary "catalog"
end
```

The `on_macos`/`on_linux` and `on_intel`/`on_arm` blocks let one cask
file handle all four platform variants. Homebrew reads only the block
that matches the current machine.

Do not edit cask files by hand. They are generated and pushed by each
tool's release process (see below).

## How a release updates this tap

Each tool's own repository handles its own release. When a new version
is tagged, the release process:

1. Builds cross-platform binaries.
2. Creates a GitHub Release and uploads the archives.
3. Computes SHA-256 checksums for each archive.
4. Generates an updated cask file and commits it here.

The connection between a tool's repository and this tap is configured in
the tool's release tooling — not in this repository.

### The catalog example: GoReleaser

`catalog` is a Go program that uses a program called
[GoReleaser](https://goreleaser.com/) to handle the publishing of the
cask to this tap on each release. The relevant section of
[`catalog`'s `.goreleaser.yaml`](https://github.com/westarete/catalog/blob/main/.goreleaser.yaml)
is the `homebrew_casks` block:

```yaml
homebrew_casks:
  - name: catalog
    binaries:
      - catalog
    homepage: https://github.com/westarete/catalog
    description:
      Generates and maintains CATALOG.md — a behavior file that tells an
      AI agent when to pull each document into context.
    repository:
      owner: westarete
      name: homebrew-tap
      branch: main
      token: "{{ .Env.HOMEBREW_TAP_TOKEN }}"
    commit_author:
      name: goreleaserbot
    commit_msg_template: "Update catalog to {{ .Tag }}"
```

GoReleaser reads that block, fills in the version and checksums it just
computed, writes `Casks/catalog.rb`, and pushes the commit to this repo.
See the
[GoReleaser homebrew casks documentation](https://goreleaser.com/cookbooks/homebrew-casks/)
for all available options.

Other tools in this tap can use whatever release tooling fits their
stack — GoReleaser, a GitHub Actions script, or anything else — as long
as it writes a valid cask file and pushes it here.

## Authentication and authorization

A tool's release process runs in GitHub Actions and needs permission to
push commits to this repository. That is handled with a fine-grained
GitHub Personal Access Token (PAT) scoped as narrowly as possible.

### Creating the token

1. Go to **GitHub → Settings → Developer settings → Personal access
   tokens → Fine-grained tokens → Generate new token**.
2. Set the resource owner to `westarete`.
3. Under "Repository access," choose "Only select repositories" and pick
   `westarete/homebrew-tap`.
4. Under "Permissions," grant **Contents: Read and write**. No other
   permissions are needed.
5. Set a reasonable expiration and copy the token — you won't see it
   again.

### Storing the token

Add the token as a repository secret in the **tool's own repository**
(not this tap). In `westarete/catalog`, the secret is named
`HOMEBREW_TAP_TOKEN`. The Actions workflow passes it to GoReleaser via
the environment, where the `.goreleaser.yaml` reads it as
`{{ .Env.HOMEBREW_TAP_TOKEN }}`.

Storing the token in the tool's repo means:

- This tap holds no secrets.
- Each tool's maintainer controls their own token lifecycle.
- Rotating a token only requires updating one repository secret.

### What the token authorizes

The token allows the tool's CI to push a single commit to this tap's
`main` branch. It cannot read or write anything else on GitHub.

## This is a public repository

This tap and the tools in it are open source. Commits, cask files,
release notes, and tool descriptions are all publicly visible. When
writing descriptions, commit messages, or documentation, write for a
general audience — someone who has never heard of West Arete should be
able to understand what a tool does and how to use it. Avoid referencing
internal project names, client work, or West Arete infrastructure that
wouldn't mean anything to an outside reader.

## Adding a new tool to this tap

1. Set up your tool's release process to generate a cask file and push
   it to `Casks/<toolname>.rb` in this repo.
2. Create a fine-grained PAT as described above and store it as a secret
   in your tool's repository.
3. Tag a release. The release process should commit the new cask file
   here automatically.
4. Verify with `brew install westarete/tap/<toolname>`.

There is no manual step in this tap's repository beyond reviewing the
first cask commit when it arrives.

## GitHub Actions workflows

This tap has one workflow: `.github/workflows/tests.yml`, which runs
`brew test-bot --only-tap-syntax` on every push and pull request. It
checks that all cask files are valid Ruby and conform to Homebrew's cask
conventions.

### Supported platforms

CI runs on Apple Silicon (`macos-26`) and Linux (`ubuntu-latest`). Intel
Mac (`macos-15-intel`) is not in the matrix — Intel x86_64 is being
phased out by Homebrew and we don't use Intel Macs. Add it back if you
need to verify Intel compatibility.

### What's deliberately absent

`brew tap-new` also generates a `publish.yml` workflow for building and
publishing _bottles_ — pre-compiled archives of source formulae. This
tap has no formulae, so there is nothing to bottle and that workflow was
omitted.

### If you add a formula

If this tap ever gains a formula under `Formula/`, add back the two
steps that `brew tap-new` would have generated:

1. In `tests.yml`, add `brew test-bot --only-formulae` (conditional on
   `pull_request`) and an artifact upload step for `*.bottle.*`.
2. Add `.github/workflows/publish.yml` — the `brew pr-pull` workflow
   that fires when a PR is labeled `pr-pull`. It downloads the built
   bottles, commits them, pushes, and deletes the source branch.

The canonical source for both is `brew tap-new <user>/<repo>`, which
generates up-to-date versions of these files. Run it in a scratch
directory, compare the output to what's here, and copy in whatever has
changed.

## Markdown style

All Markdown in this repository is formatted with
[Prettier](https://prettier.io/) and checked with
[markdownlint-cli2](https://github.com/DavidAnson/markdownlint-cli2).
Before committing any `.md` changes, run:

```sh
npx prettier --write "**/*.md"
npx markdownlint-cli2 "**/*.md"
```

Both must pass with no errors before committing.
