# Add an extension

To add a theme, go to [Add a theme](#add-a-theme).

You add an extension with a pull request. The pull request adds one file: `entries/<name>.json`. The code stays in your own repository.

## 1. Put the files in a public GitHub repository

- Use a public repository on GitHub.
- Put the files of the drop-in at the top level, or in one folder. That folder is `source.path` in the entry.
- Use the same file names as in the drop-in folder (`~/.pi-pocket/extensions/`):
    - `<name>.ts`: the module. This file is necessary.
    - `<name>.js` or `<name>.mjs`: a browser module beside it (optional).
    - `<name>/…`: other files in a folder with the name of the module (optional). Four folders deep at most. No hidden files and no `node_modules`. A name must not end with a dot, and must not be a Windows device name (for example `con`, `nul`, or `com1`). A file must not have the name of a folder that holds other files.
- Push the files. Write down the full commit ID (40 characters). Short IDs are not permitted.

For how to write a drop-in, read [Writing extensions](https://github.com/TannerMidd/pi-pocket/blob/main/docs/extensions.md) in Pi Pocket.

## 2. Get the entry

**The easy way:** open Pi Pocket. Go to **Menu → Extensions**, tap your drop-in, and tap **Share…**. Pi Pocket makes the entry with the sizes and hashes of the files. Then it opens the GitHub page for a new file in this repository.

**By hand:** copy the example in the [README](README.md#an-entry). Get the size and the hash of each file:

```sh
wc -c acme-clock.ts
sha256sum acme-clock.ts
```

## 3. Complete the entry

Make sure that these fields are correct:

- `source.repo`: your repository, as `owner/name`.
- `source.commit`: the full commit ID from step 1.
- `source.path`: the folder of the files, or `""` for the top level.
- `tags`: up to 10 short words, such as `time` or `git`.
- `author.github`: your GitHub user name.

The README tells about [each field](README.md#an-entry).

## 4. Open a pull request

1. Add the file `entries/<name>.json`. The file name must be the `name` in the entry.
2. Open a pull request. In the description, tell what the extension does and why it needs each tool, hook, and route.
3. Do not edit `index.json`. CI makes it after the merge.

## What CI checks

- The entry agrees with the JSON Schema in `schema/`.
- Pi Pocket's own reader accepts the entry.
- The file name is `<name>.json`.
- No other entry has the same module, extension, or tool name.
- The files at the pinned commit have the sizes and SHA-256 hashes in the entry.
- The module loads in a test Pi Pocket, without errors.
- `declares` is the same as what the module installs.

For a pull request, CI downloads and loads only the entries that the pull request changes. It checks the format of all entries.

CI does not do a security review.

## What maintainers check

A maintainer reads all the code at the pinned commit before a merge. The maintainer looks for these:

- The code does only what the description says.
- The code does not send data to other places, unless the description says so and the owner can control it.
- The code reads secrets or other sessions only when its description says why it must.
- The code does not download and run code that the commit does not pin. It can install what it needs (packages, a runtime, or a model) only when a lock file in the entry pins each package by version and hash and the installer enforces the hashes, the description names what it installs and from where, and it installs only after the owner acts, never while it loads ([G14](https://github.com/TannerMidd/pi-pocket/blob/main/docs/extensions/policies/gallery-review.md#what-gets-refused)).
- The tool names and descriptions are clear, and `readOnlyHint` is true only for tools that change nothing.
- The code follows the rules in [Writing extensions](https://github.com/TannerMidd/pi-pocket/blob/main/docs/extensions.md#rules-that-are-easy-to-break).

A maintainer can close a pull request. The maintainer does not always give a reason.

## Update an extension

1. Push the new code to your repository.
2. Change `source.commit` to the new commit ID.
3. Change `size` and `sha256` of each file that changed. Add and remove files in `files` if necessary. **Share…** in Pi Pocket makes all of this for you.
4. Change `version`, and `declares` if the module installs different things.
5. Open a pull request.

A maintainer reads the changes before the merge. Pi Pocket does not install updates automatically: each owner sees the diff and chooses.

## Names

Give each extension and each tool a name of its own, with a prefix, such as `acme-clock` and `acme_clock`.

You cannot use these names:

- A module `name` that starts with `pocket-`.
- The name of a built-in module: `prompt`, `artifacts`, `browser`, `subagents`, `agent-teams`, `schedules`, `goals`, `plan`, `guard`, `codemode`, `memory`, `pip`, `triggers`, and `gallery`.
- The extension name `coding-tools`, or an extension name that starts with `pocket-`.
- A built-in tool name: `read`, `write`, `edit`, `bash`, `artifact`, `browser`, `subagent`, `team`, `schedule`, `codemode`, `memory`, `sessions`, `session_read`, `session_start`, `session_send`, `session_follow`, `session_stop`, `session_archive`, `session_schedule`, `approvals`, `watch_pr`, `repo_watch`, and `session_watch_pr`.
- A module, extension, or tool name that an entry in the index has now. The first entry keeps the name.

A module `name` has 2 to 64 characters: `a-z`, `0-9`, `-`, and `_`. The first character is a letter or a digit.

## Limits

- 50 files for each extension.
- 512 KiB for each file.
- 2 MiB for all the files of an extension.
- Text fields are one line, with the lengths in the [README](README.md#an-entry).

## Add a theme

You add a theme the same way: a pull request adds one file, `entries/<name>.json`, and the files stay in your own repository.

1. Make the theme in Pi Pocket (Appearance → **Duplicate**, then **Edit with Pi**). Its folder is `~/.pi-pocket/themes/<name>/`.
2. Put the folder in a public GitHub repository, with the same name: `<name>/theme.json`, `<name>/theme.css`, and so on. Put it at the top level, or in a folder that you write in `source.path`. Push it, and write down the full commit ID.
3. Get the entry. **The easy way:** in Pi Pocket, choose the theme in **Appearance** and tap **Share…**. Pi Pocket does its theme check, and makes the entry with the files that the theme loads, with their sizes and hashes. Give the theme a `description` in `theme.json` first. **By hand:** copy the example in the [README](README.md#a-themes-entry).
4. Complete the entry as for an extension (step 3 above), and open a pull request. In the description, tell what the theme changes.

A theme's `name` has 2 to 48 characters: `a-z`, `0-9`, and `-`. It must not be an id that Pi Pocket keeps: `desktop`, a theme that comes with Pi Pocket (`pocket`, `lantern`), or one of its palettes (such as `tokyo-night`). No other entry can have it.

### What CI checks for a theme

- The entry agrees with `schema/theme.schema.json`, and Pi Pocket's own reader accepts it.
- The files at the pinned commit have the sizes and SHA-256 hashes in the entry.
- Pi Pocket's theme check accepts the files: `theme.json`, `colors.toml`, and `colors-light.toml` are correct, and the CSS loads only files of the theme, by relative paths. CI does not run a load test, because a theme runs no code.

### What maintainers check for a theme

A maintainer reads all the CSS at the pinned commit before a merge. The maintainer looks for these:

- The theme does what the description says.
- No rule hides, covers, or changes approval cards, notices, or the sign-in page, and no rule uses `content:` to put other words on the app.
- No font draws other letters than the ones it names.
- Focus rings stay visible.
