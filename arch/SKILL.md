---
name: arch
description: >
  Administer and customize installed Arch Linux systems. Use for pacman or AUR
  package work, systemd services, boot and kernel maintenance, hardware diagnosis,
  and Arch-hosted desktop configuration including Hyprland. Do not use for other
  distributions or for developing Arch Linux packages unless the task also changes
  the running Arch system.
---

# Arch Linux System Skill

Manage an installed Arch Linux system without assuming a particular installer,
desktop environment, bootloader, filesystem, AUR helper, or dotfiles layout.

This skill adapts the evidence-first customization approach of Omarchy's agent
skill to vanilla Arch. Omarchy-specific commands, paths, Lua helpers, and shell
components are intentionally not available here.

## Route to the Relevant Guide

Read only the guide that matches the task:

- Package installation, upgrades, removals, AUR, mirrors, keyrings, or `.pacnew`: [references/packages.md](references/packages.md)
- Services, timers, journals, sessions, or persistent unit changes: [references/systemd.md](references/systemd.md)
- Hyprland bindings, monitors, window rules, autostart, or compositor behavior: [references/hyprland.md](references/hyprland.md)
- Kernels, initramfs, bootloaders, EFI, filesystems, encryption, or recovery: [references/boot-and-storage.md](references/boot-and-storage.md)
- Themes, terminals, bars, launchers, portals, audio, screenshots, or recording: [references/desktop.md](references/desktop.md)
- Pacman hooks, user services, scheduled tasks, or event automation: [references/automation.md](references/automation.md)

## Establish the Actual System First

Do not prescribe changes from the word "Arch" alone. Collect only the facts
needed for the task, starting with read-only commands:

```bash
cat /etc/os-release
uname -r
pacman -Q pacman systemd
pacman -Qm
systemctl --failed
systemctl --user --failed
```

For desktop work, also inspect the active session and relevant configuration:

```bash
printf 'session=%s desktop=%s\n' "$XDG_SESSION_TYPE" "$XDG_CURRENT_DESKTOP"
loginctl session-status
find ~/.config -maxdepth 2 -type f 2>/dev/null | sort
```

For boot or storage work, use the dedicated guide before changing anything.

Treat Arch derivatives separately. CachyOS, EndeavourOS, Garuda, and Manjaro may
have different repositories, kernels, helpers, or release timing. Confirm the
distribution and repository configuration instead of applying vanilla Arch
instructions blindly.

## Safety Invariants

1. Never create a partial upgrade. Do not run or recommend `pacman -Sy`,
   `pacman -Sy <package>`, or refresh databases without completing a compatible
   full upgrade. Use `pacman -Syu` for the normal upgrade path.
2. Prefer read-only diagnosis before mutation. Preserve the exact error and logs;
   do not hide relevant stderr or reinstall packages at random.
3. Do not edit package-owned files under `/usr`. Put user configuration under
   `~/.config`, use `/etc` for system configuration, and use supported override
   mechanisms such as systemd drop-ins.
4. Before editing an existing config, inspect how it is loaded and preserve its
   format, permissions, and surrounding conventions. Back up important untracked
   files before risky or broad changes.
5. Treat the AUR as untrusted build input. Inspect `PKGBUILD`, `.install` files,
   patches, sources, and recent changes before building. Never run an AUR helper
   or `makepkg` as root.
6. Before kernel, bootloader, initramfs, encryption, partition, or filesystem
   changes, identify the whole boot chain and retain a tested recovery path.
   Never remove the only known-good kernel or boot entry.
7. Snapshots are rollback aids, not backups. Do not imply that a snapshot protects
   against disk loss, filesystem-wide damage, or mistakes outside its subvolume.
8. Use current primary documentation for volatile syntax and compatibility facts.
   Prefer the [ArchWiki](https://wiki.archlinux.org/) and the relevant upstream
   project's official documentation over blogs or remembered commands.
9. Do not assume permission for package installation, privileged writes, service
   changes, rebooting, or destructive cleanup merely because diagnosis was requested.

## Decide at the Correct Layer

- **Package state:** identify repository vs foreign packages and resolve upgrade,
  keyring, mirror, or file-conflict problems before debugging applications built
  against potentially stale libraries.
- **Service state:** distinguish system units from user units. Inspect the unit,
  its drop-ins, and the current-boot journal before editing it.
- **User configuration:** edit the file already loaded by the application. Do not
  invent an alternate config tree or replace the user's dotfile layout.
- **Hardware:** identify the device, driver, firmware, and running kernel before
  changing modules or installing another driver stack.
- **Boot and storage:** identify the ESP, bootloader, kernel package, initramfs
  generator, root layout, and encryption boundary as one system.

Change one layer at a time and retest the real failure after each meaningful change.

## Package-Owned vs User-Owned Configuration

Use `pacman -Qo /path/to/file` to determine whether a file belongs to a package.
Inspect packaged defaults without modifying them:

```bash
pacman -Qo /path/to/file
pacman -Ql package_name
pacman -Qii package_name
```

When a package supplies a default under `/usr/share` or `/usr/lib`, copy or
override it only through the application's documented user or system mechanism.
Do not create a copy until its load path and precedence are verified.

## Verification

Choose checks that exercise the changed layer:

- Packages: `pacman -Q package_name`, `pacman -Qk package_name`
- System services: `systemctl status unit_name`, `journalctl -u unit_name -b`
- User services: `systemctl --user status unit_name`, `journalctl --user -u unit_name -b`
- Hyprland: `hyprctl reload`, then `hyprctl configerrors`
- Bootloader: inspect its status and generated artifacts without rebooting first
- Configuration: run the application's parser, check command, or live status view

Report what changed, where it changed, how it was verified, and whether a reboot,
relogin, or manual follow-up remains. Do not claim success from a clean text edit
alone.
