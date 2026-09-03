# Packages, Upgrades, and the AUR

Read this for pacman, repository packages, foreign packages, mirrors, keyrings,
file conflicts, `.pacnew`, or AUR work.

## Inspect Before Changing

```bash
pacman -Q
pacman -Qm
pacman -Qdt
pacman -Qu
checkupdates
```

`checkupdates` comes from `pacman-contrib` and checks against temporary sync
databases without altering the system databases. If it is unavailable, do not
install it merely for diagnosis; `pacman -Qu` only reflects the databases already
present and may therefore be stale.

`pacman -Qm` lists packages absent from the configured sync databases; they may
be AUR packages, manually built packages, or packages removed from a repository.
Do not call all of them AUR packages without checking their origin.

## Repository Package Operations

Normal full upgrade:

```bash
sudo pacman -Syu
```

Install from configured repositories while performing a compatible upgrade:

```bash
sudo pacman -Syu --needed package_name
```

Search and inspect:

```bash
pacman -Ss search_term
pacman -Si package_name
pacman -Qs search_term
pacman -Qi package_name
pacman -Ql package_name
pacman -Qo /path/to/file
```

Before removing a package, inspect reverse dependencies and explicit install
status. `pacman -Rns` can remove dependencies and system configuration; show the
transaction and confirm its scope rather than treating it as a universal default.

## Prohibited Partial-Upgrade Patterns

Do not use:

```bash
pacman -Sy
pacman -Sy package_name
```

Refreshing sync databases while keeping older installed libraries can make newly
installed packages incompatible with the running system. If a full upgrade is
currently unsafe or impossible, stop and resolve that blocker instead of mixing
repository states.

## File Conflicts

Use the exact paths from pacman's error. Determine ownership with `pacman -Qo` and
whether the file was created manually or by another package. Do not recommend a
blanket `--overwrite '*'`. A narrowly scoped overwrite is a last resort after the
source of the conflict is understood.

## `.pacnew` and `.pacsave`

Package upgrades do not merge local configuration automatically. Find pending files:

```bash
sudo pacdiff -o
```

Review and merge each file semantically. Do not replace the active configuration
wholesale without preserving local settings. `pacman-contrib` provides `pacdiff`.

## AUR Workflow

An AUR helper is convenience, not a trust boundary. Before installing or updating:

1. Fetch the current PKGBUILD and related files.
2. Read `PKGBUILD`, any `.install` file, patches, and source URLs.
3. Check unexpected binary downloads, privilege use, generated services, and
   maintainer or upstream changes.
4. Build as an unprivileged user with `makepkg`; let pacman handle only the final
   package transaction requiring privilege.

Never run `makepkg` or an AUR helper with `sudo`. When diagnosing helper behavior,
fall back to the underlying `git`, `makepkg`, and `pacman -U` steps so the actual
failure remains visible.

## Mirrors and Keyrings

Separate causes before changing configuration:

- HTTP or timeout errors can be a mirror or network problem.
- Signature failures can reflect stale system time, an outdated keyring, damaged
  local trust state, or a repository problem.
- A newly synchronized mirror paired with stale local packages still does not
  justify a partial upgrade.

Prefer current Arch news and ArchWiki recovery procedures for keyring or repository
incidents. Do not delete the pacman keyring or package database as a first response.

## Cleanup

Treat orphan and cache cleanup as destructive maintenance. Review candidates first:

```bash
pacman -Qdt
paccache -d
```

Keep rollback needs in mind, especially around kernel, graphics, and boot packages.
