# Ghostty Config Developer Notes

Personal configuration for the Ghostty terminal (1.4.0 stable, installed as a Homebrew cask) on macOS. Everything lives in one file, `config.ghostty`, which Ghostty reads from `$XDG_CONFIG_HOME/ghostty/` (`~/.config/ghostty/` when the variable is unset, as it is on this machine). The config copies the look and behaviour of the WezTerm setup (see Related Repos). Start by reading `config.ghostty`; its comments explain every value that is not obvious.

This folder is not a git repository yet, so there is no history to fall back on and no commit flow (see Pull Requests).

## Related Repos

| Repo | Folder | What it is | Takes effect by |
|---|---|---|---|
| Ghostty config (this folder) | `~/.config/ghostty` | The terminal config, mirrored from WezTerm | `Command+Shift+,` in Ghostty (`reload_config`) |
| WezTerm config | `~/.config/wezterm` | The original config this one copies (`wezterm.lua`) | WezTerm reloads it on save |

Rules for changes that cross the two:

- **WezTerm is the source of the look.** Theme, font family, weight and size, window padding and the close confirmation were copied from `wezterm.lua`. When one of these changes in either folder, ask whether the other should follow. Never change the other folder without that answer.
- **The background differs on purpose.** WezTerm shows a dimmed wallpaper; Ghostty has a see-through, blurred background (`background-opacity`, `background-blur`) and no image. Do not copy one background to the other unless asked.

## Documentation Policy

Before using a config key, keybind action or CLI action, read its documentation instead of relying on memory:

1. First: the docs built into the installed app, which always match the installed version: `ghostty +explain-config <key-or-action>` and `ghostty +show-config --default --docs`. Check the version with `ghostty +version`.
2. Then: the website, https://ghostty.org/docs. It follows the latest release, so check each option's "Available since" line against the installed version.
3. Backup: context7 MCP (`resolve-library-id`, then `query-docs`).

| Part | Docs |
|---|---|
| Ghostty 1.4 (installed app) | `ghostty +explain-config <key>`, `ghostty +show-config --default --docs` |
| Docs home | https://ghostty.org/docs |
| Config file: locations, syntax, load order | https://ghostty.org/docs/config |
| Option reference | https://ghostty.org/docs/config/reference |
| Keybindings | https://ghostty.org/docs/config/keybind |
| Themes | https://ghostty.org/docs/features/theme |
| Source (option definitions in `src/config/Config.zig`) | https://github.com/ghostty-org/ghostty |

## Project Docs

There is no `docs/` folder and no `README.md`. Plans and investigation notes follow the global `reed-docs/` rule.

- This `CLAUDE.md` and the comments inside `config.ghostty` are the only docs, and both are **Living reference**. A change to a value whose comment explains it updates that comment in the same change.
- Root files that tools look for: this `CLAUDE.md` only. No UI, so no `PRODUCT.md` or `DESIGN.md`.

## Commands and Safety

Run commands from this folder. When Claude Code runs inside Ghostty, `ghostty` is on the PATH; from any other terminal use the full path, `/Applications/Ghostty.app/Contents/MacOS/ghostty`.

```sh
ghostty +validate-config                         # check the config Ghostty would load; prints nothing and exits 0 when clean
ghostty +validate-config --config-file=<path>    # check one file, for example a draft, before it replaces config.ghostty
ghostty +explain-config <key-or-action>          # docs for one option or keybind action, for the installed version
ghostty +show-config --default --docs            # every option with its default and its docs
ghostty +list-themes --plain                     # theme names, built in and custom
ghostty +list-fonts --family="<family name>"     # how Ghostty resolves a font family, in priority order
ghostty +show-face --string=a                    # which font face actually draws a character
ghostty +list-keybinds --default                 # the default keybinds
```

- **Check every edit in three steps**, the config's version of the three test levels. Unit: `+validate-config` exits 0 with no output; it catches unknown keys and invalid values, with file and line. Integration: the lookup commands confirm the value resolves to something real (`+list-themes` for a theme name, `+list-fonts --family` and `+show-face` for a font). End to end: reload Ghostty and look at a window. `+validate-config` does not check that a theme, font or image exists, so step one alone is not enough.
- **Run `+validate-config` alone in a Claude Code shell call, or pipe it through `cat`.** Writing straight to Claude Code's captured output, it erases everything printed earlier in the same call (seen on 1.4.0). Its own output and exit code survive.
- **Plain `+list-fonts` hides the configured font.** It lists `FiraCode Nerd Font Mono` but not `FiraCode Nerd Font`, which is the family the config uses and which Ghostty does load. Check a font with `--family` and `+show-face`, not with the plain list.
- **Reloading is manual.** `Command+Shift+,` reloads the config; nothing reloads it on save. Some options only reach new windows or tabs, and some (for example `background-opacity` on macOS) need a full quit and restart of Ghostty. `+explain-config <key>` says which.
- **Back up before editing** while this folder has no git: `cp config.ghostty "config.ghostty.bak-$(date +%Y%m%d-%H%M%S)"`, the same naming the WezTerm folder uses.
- No database, network service or credential is involved, so nothing here can touch production.

## Pull Requests

No git and no pull request flow yet. Until there is git, these rules apply to each edit, with the backup above in place of a commit.

- One concern per edit. A theme change and a keybind change never share one.
- A fix is checked with the step that would have caught the problem (see "Check every edit in three steps").
- A changed value whose comment explains it gets an updated comment in the same edit.

## Project Contracts

**Comments go on their own line.** Ghostty only accepts `#` at the start of a line. A comment after a value on the same line becomes part of the value.

```
# Why this value: one or two lines.
font-size = 18
```

**Every value that is tuned, copied or surprising carries a comment above it saying why.** The current file does this for the WezTerm mirror and the background opacity, blur and colour. Keep it that way, so a later edit does not "fix" a value that was chosen on purpose.

**One config file, in one place.** Ghostty loads, in order, `config.ghostty` and `config` from this folder, then the same two names from `~/Library/Application Support/com.mitchellh.ghostty/`; a later file overrides an earlier one. Only `config.ghostty` here exists (the name needs Ghostty 1.2.3 or later). Never create `config` here or any file in the Application Support folder, because a value set in two files is decided by load order, not by what you read.

**Custom themes go in `themes/` inside this folder.** Ghostty looks there and in its own built-in themes folder. Reference a custom theme by its file name, as with `theme = Nord`.

## Design

Project-level answers to the global Design Patterns rules.

### Seams

None yet. If the file grows enough to split (for example keybinds in their own file), use Ghostty's `config-file` key with a path relative to this folder. Files named by `config-file` load only after the whole including file, so a value set in an included file wins over the same key in `config.ghostty`. Split only when there is a real need.

### Review vocabulary

- "Two sources of truth": one setting in two places (two config files, or a key repeated in one file), so load order decides which one wins.
- "Magic number": a tuned value without a comment saying where it came from.

### Not applicable here

- Dependency families and fakes: no code and no side-effecting clients; the external pieces are the installed fonts and the Ghostty app itself.
- Layers and import direction, Where rules run, Events, Status lifecycles, State coordination, Framework hook points: a single flat config file, no code.
- Authorization, Per-request state on the server: single-user local config, no server.
- Optimistic writes and persisted state: the config stores nothing; Ghostty keeps its own window state.
- Result type: no code; config errors come from `+validate-config`.

## Keeping This File Current

- New option that is surprising or needs a restart: note it in Commands and Safety or Project Contracts.
- Ghostty upgrade (`brew upgrade --cask ghostty`): update the version in the first paragraph and the Documentation Policy table, then run the three checks.
- A path, command or URL in this file stops existing: fix it in the same change that removed it.
- Paths here use `~/.config` or this folder, never one person's home folder spelled out. If the folder becomes a git repository, rewrite Pull Requests and drop the backup rule in the same change.
