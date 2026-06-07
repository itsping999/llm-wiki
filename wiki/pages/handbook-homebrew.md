---
title: "Homebrew Cheatsheet"
slug: "handbook-homebrew"
type: source
created: "2026-06-07T06:23:38Z"
updated: "2026-06-07T06:23:38Z"
sources:
  - "raw/sources/handbooks/Homebrew/Homebrew Handbook.md"
---

> Source snapshot: [raw/sources/handbooks/Homebrew/Homebrew Handbook.md](<../../raw/sources/handbooks/Homebrew/Homebrew Handbook.md>)

# Homebrew Cheatsheet

## Install

```bash
# if not installed, install command line tools
xcode-select --install

# install homebrew
/bin/bash -c "$(curl -fsSL <https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh>)"

```

## Command

```bash
# install a package
brew install git

# install a cask
brew install --cask google-chrome

# uninstall a package
brew uninstall git

# uninstall a cask
brew uninstall --cask google-chrome

# search packages
brew search git

# search cask packages
brew search --cask google-chrome

# upgrade package
brew upgrade git

# unlink package
brew unlink git

# link package
brew link git

# switch package version
brew switch git 1.0.0

# list the installed versions of package
brew list --versions git

# list installed package
brew list

# list installed formulae
brew list --formulae

# list installed command
brew list --cask

# list brew config
brew config

```

## Help

```bash
# print help information
brew --version

# print help info for a brew command
brew help <sub-command>

# check system for potenial problems
brew doctor

```

## Updates

```bash
# fetch latest version of homebrew and formulae
brew update

# show formulae with an updated version available
brew outdated

# upgrade all outdated and unpinned brews
brew upgrade

# upgrade only the specified brew
brew upgrade <formulae>

# prevent the specified formulae from being upgraded
brew pin <formulae>

# allow the specified formulae to be upgraded
brew unpin <formulae>

```

## Repositories

```bash
# list all the current tapped eepositories
brew tap

# tap a formulae repository from github using https for <https://github.com/user/homebrew-repository>
brew tap <user/repo>

# tap a formulae repository from the specified URL
brew tap <user/repo> <URL>

# remove the given tap from the repository
brew untap <user/repo>

```

## Cleanup

```bash
# remove older versions of installed formulae
brew cleanup

# remove older versions of specified formulae
brew cleanup <formulae>

# display all formulae that will be removed(dry run)
brew cleanup -n

```

## Export / Import

```bash
# export installed packages
brew bundle dump --describe --force --file="~/brewfile"

# import installed packages
brew bundle --file="~/brewfile"

```

## Official Docs & Extensibility

- Official docs
  - Homebrew documentation: [https://docs.brew.sh/](https://docs.brew.sh/)
  - Formula cookbook: [https://docs.brew.sh/Formula-Cookbook](https://docs.brew.sh/Formula-Cookbook)
  - Cask reference: [https://docs.brew.sh/Cask-Cookbook](https://docs.brew.sh/Cask-Cookbook)

- High-frequency entry points
  - `brew install`, `brew upgrade`, `brew cleanup`, and `brew doctor` are the daily commands.
  - `brew list --formula` vs `brew list --cask` separates CLI tools from GUI apps.
  - `brew services start/stop/list` manages background services (Redis, PostgreSQL, Nginx, etc.).

- Extensible directions
  - Add tap management for third-party formulae and custom repositories.
  - Cover `brew bundle` and Brewfile for reproducible environment setup.
  - Add troubleshooting: broken links, permission issues, and Rosetta 2 considerations on Apple Silicon.
