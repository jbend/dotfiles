# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A personal dotfiles repo managed by [chezmoi](https://www.chezmoi.io/). This directory is the chezmoi **source state** (`~/.local/share/chezmoi`); chezmoi renders it into `$HOME`. There is no build, lint, or test suite.

## Commands

```sh
chezmoi managed            # list which target files chezmoi actually tracks
chezmoi diff               # preview what `apply` would change in $HOME
chezmoi apply -v           # write source state into $HOME
chezmoi add ~/.foo         # import an existing home file (creates dot_foo here)
chezmoi edit ~/.foo        # edit the source file for a target
chezmoi execute-template < file.tmpl   # test-render a template
```

Always run `chezmoi diff` before `chezmoi apply`; apply overwrites files in `$HOME`.

## chezmoi naming rules

- Source files use chezmoi prefixes: `dot_zshrc` → `~/.zshrc`, `.tmpl` suffix for templates, `private_`/`executable_` for permissions. Source files starting with a literal `.` are ignored, so never add files that way — use `chezmoi add`.
- `.chezmoiignore` lists **target** paths that must not be deployed: repo docs (`README.md`, `CLAUDE.md`) and `.zshrc` (zsh is kept in the repo but intentionally not deployed; remove the line to enable it).

## Contents

- `dot_gitconfig.tmpl` → `~/.gitconfig`: identity, `gh auth git-credential` (resolved from PATH, since gh is installed via mise and versioned paths go stale), log aliases (`lg`, `new`, `missing`, `glt`, `glta`), `zdiff3` merge style. The delta pager section renders only when `delta` is on PATH (`lookPath`), so after installing `git-delta` run `chezmoi apply` again.
- `dot_zshrc`: zinit + Powerlevel10k setup; currently ignored (see above).

## Git config layering on this machine

Omarchy ships `~/.config/git/config` (not managed here) with its own defaults (pull.rebase, rerere, diff.algorithm, etc.). Git reads it **before** `~/.gitconfig`, so values in this repo override it. Multi-valued keys like `credential.helper` accumulate; the empty `helper =` line resets the list so only the chezmoi-managed helper runs.

## Planned

Commit signing and SSH are intentionally not configured yet; they will move to 1Password (SSH agent + SSH-based commit signing). Don't re-add the old GPG / Windows `ssh.exe` settings from git history.
