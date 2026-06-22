# westarete/tap

This is [West Arete](https://westarete.com/)'s Homebrew tap — a
collection of tools we build and distribute via Homebrew.

## Usage

```sh
brew tap westarete/tap
brew install westarete/tap/catalog
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

We don't edit those cask files by hand. Each tool's own repository handles its own release process. When a new
version is tagged, that process builds the binaries, creates a GitHub
Release, and opens a commit to this repo updating the cask with the new
version and checksum. Homebrew picks it up automatically the next time
someone runs `brew upgrade`.

For example, `catalog` is a Go program that uses
[GoReleaser](https://goreleaser.com/). When a release is tagged in
`westarete/content-catalog`, GoReleaser builds cross-platform binaries
and writes the updated cask to `Casks/catalog.rb` here. The connection
is configured in that repo's `.goreleaser.yaml` under `homebrew_casks`.
Other tools in this tap will use whatever release tooling fits their
stack.
