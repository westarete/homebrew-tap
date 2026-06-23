# westarete/tap

This is [West Arete](https://westarete.com/)'s Homebrew tap — a
collection of tools we build and distribute via Homebrew.

## What's in this tap

### [catalog](https://github.com/westarete/catalog): context loading optimization

Once you have 20 or more files in a repository, agents do a pretty poor
job of deciding which files to load into context. They'll randomly load
ones files they don't need, and they won't load the ones that they do
need.

`catalog` is a command-line tool that profiles the documents in your
repo provides agents with a catalog so they can load precisely what they
need. This means better answers, faster responses, and more context
window left for the actual work.

## Usage

```sh
brew tap westarete/tap
brew trust westarete/tap
brew install catalog
```

Or install a tool directly without tapping first:

```sh
brew install westarete/tap/catalog
```

## How this works

A [Homebrew tap](https://docs.brew.sh/Taps) is just a Git repository
that Homebrew knows how to read. Each tool in this tap has a cask file
under `Casks/` — a small Ruby file that tells Homebrew where to download
the binary and how to install it.

We don't edit those cask files by hand. Each tool's own repository
handles its own release process. When a new version is tagged, that
process builds the binaries, creates a GitHub Release, and opens a
commit to this repo updating the cask with the new version and checksum.
Homebrew picks it up automatically the next time someone runs
`brew upgrade`.

For example, `catalog` is a Go program that uses
[GoReleaser](https://goreleaser.com/). When a release is tagged in
`westarete/catalog`, GoReleaser builds cross-platform binaries and
writes the updated cask to `Casks/catalog.rb` here. The connection is
configured in that repo's `.goreleaser.yaml` under `homebrew_casks`.
Other tools in this tap will use whatever release tooling fits their
stack.

## Authorizing writes to this repo

Each tool's release process needs permission to push cask updates here.
That permission comes from a fine-grained GitHub Personal Access Token
(PAT) stored as a secret in the tool's own repository.

To set this up for a new tool:

1. Go to GitHub → Settings → Developer settings → Personal access tokens
   → Fine-grained tokens → Generate new token.
2. Set the resource owner to `westarete`, restrict repository access to
   `westarete/homebrew-tap` only, and grant Contents read/write
   permission.
3. Copy the token and add it as a repository secret in the tool's repo
   (e.g. `westarete/catalog`). Name it `HOMEBREW_TAP_TOKEN` or whatever
   the release config expects.

The token is stored in the tool's repo so the Actions workflow can use
it on every release, regardless of who triggers the release.
