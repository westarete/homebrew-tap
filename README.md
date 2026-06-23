# westarete/tap

This is [West Arete](https://westarete.com/)'s Homebrew tap — a
collection of tools we build primarily for our own use and release as
open source for anyone who finds them useful.

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

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how this tap works and how to
add a new tool.
