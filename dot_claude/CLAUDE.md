# Personal instructions (all projects)

Managed by chezmoi: edit `~/.local/share/chezmoi/dot_claude/CLAUDE.md`, then `chezmoi apply`. The dotfiles repo is **public**.

## Machine

- Omarchy (Arch Linux + Hyprland). Dotfiles via chezmoi at `~/.local/share/chezmoi`: edit the source, run `chezmoi diff`, then `chezmoi apply`. Never edit deployed files in `$HOME` directly if chezmoi manages them (`chezmoi managed`).
- Omarchy ships `~/.config/git/config` (not managed); `~/.gitconfig` (managed) overrides it.
- Notes and SOPs live in the Obsidian vault at `~/Vault` (follow its `CLAUDE.md`). The full access SOP is `~/Vault/Areas/Accounts and Access.md`.

## Personal vs West Coast Code (WCC)

- Most work is for WCC, my company. WCC code lives under `~/Code/wcc/` and in the **WestCoastCode** GitHub org; everything else is personal.
- Git picks the identity by directory: `~/Code/wcc/` → jeff@westcoastcode.com, elsewhere → jbendixsen@gmail.com. Clone WCC repos into `~/Code/wcc/`; don't set `user.email` per repo.
- All commits are signed via the 1Password SSH agent; signing prompts for approval in 1Password. If a commit fails to sign, report it — don't disable signing or pass `--no-gpg-sign`.

## Secrets and 1Password

- 1Password is the only place secrets live. Two accounts: **WCC Business** (anything the company owns: infra, servers, API keys, client creds) and **Families** (personal). Rule: if someone taking over the role at WCC would need it, it's Business.
- In code and config, reference secrets as `op://Vault/Item/field` and inject at runtime with `op run` / `op inject`. Never write plaintext secrets to files, `.env`, or commits.
- Never print secret values into the conversation (no `op read` / `op item get --reveal` output). Checking that an item or field exists is fine.
- Never put hostnames, IPs, usernames, or other infrastructure details in the dotfiles repo; template them from 1Password (chezmoi `onepasswordRead`).
- SSH private keys live only in 1Password (agent socket `~/.1password/agent.sock`); `~/.ssh` holds public keys and config only.
