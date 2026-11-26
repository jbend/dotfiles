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

- `dot_gitconfig.tmpl` → `~/.gitconfig`: personal identity, `gh auth git-credential` (from PATH; gh is installed via mise so versioned paths go stale), aliases, `zdiff3`, `init.defaultBranch = main`. Conditional blocks: delta (if `delta` on PATH) and SSH commit/tag signing via 1Password (if `op-ssh-sign` exists; path differs on macOS). A trailing `includeIf "gitdir:~/Code/wcc/"` switches to the WCC email — keep it last so it overrides `[user]`.
- `dot_config/git/wcc.gitconfig`: WCC identity included for `~/Code/wcc/`.
- `dot_config/git/allowed_signers`: the public signing key for both emails, for local signature verification. Update it if the signing key changes.
- `dot_claude/CLAUDE.md` → `~/.claude/CLAUDE.md`: global Claude Code instructions (1Password, identity, secrets rules).
- `private_dot_ssh/config.tmpl` → `~/.ssh/config`: one `Host` block per destination pinned to a public key (`IdentityFile ~/.ssh/<name>.pub` + `IdentitiesOnly yes`); `Host *` points at the 1Password agent socket (macOS path differs). Server hosts must template hostname/user from 1Password, e.g. `{{ onepasswordRead "op://WCC Infrastructure/<item>/<field>" "westcoastcode.1password.ca" }}`.
- `private_dot_ssh/*.pub`: public keys only; private keys never leave 1Password.
- `dot_config/1Password/ssh/agent.toml`: limits the SSH agent to vaults "Private" (account "Our Family") and "WCC Infrastructure" (account "West Coast Code"). New SSH keys must go in one of those vaults.
- `dot_zshrc`: zinit + Powerlevel10k setup; currently ignored (see above).

## Git config layering on this machine

Omarchy ships `~/.config/git/config` (not managed here) with its own defaults (pull.rebase, rerere, diff.algorithm, etc.). Git reads it **before** `~/.gitconfig`, so values in this repo override it. Multi-valued keys like `credential.helper` accumulate; the empty `helper =` line resets the list so only the chezmoi-managed helper runs.

## Public repo

This repo is public. Public keys and emails are fine; secrets, hostnames, IPs, and usernames for servers are not. Template those from 1Password with `onepasswordRead "op://..."`. The access SOP (1Password accounts/vaults, SSH hosts) lives in `~/Vault/Areas/Accounts and Access.md`.

## Planned

SSH host entries for the WCC VPSes (keys rotated out of Proton Pass) and the Mac Mini. Don't re-add the old GPG / Windows `ssh.exe` settings from git history.
