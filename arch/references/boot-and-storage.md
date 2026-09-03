# Boot, Kernel, and Storage

Read this before changing kernels, initramfs, microcode, bootloaders, EFI entries,
Secure Boot, partitions, filesystems, encryption, snapshots, or resume configuration.

These changes can make the machine unbootable. Diagnosis does not authorize mutation.

## Map the Whole Boot Chain

Collect the relevant state without changing it:

```bash
cat /etc/os-release
uname -r
pacman -Q | rg '^(linux|mkinitcpio|dracut|grub|systemd|limine|refind|.*-ucode)\b'
lsblk -f
findmnt /
findmnt /boot
findmnt /efi
findmnt /boot/efi
cat /proc/cmdline
efibootmgr -v
bootctl status
```

Some commands may not exist or may report that their bootloader is not in use; that
is evidence, not a reason to install or switch tools. Determine:

- Firmware mode: UEFI or legacy BIOS
- ESP device and mountpoint
- Bootloader and where its configuration/artifacts live
- Kernel package and currently running kernel
- Initramfs generator: mkinitcpio, dracut, booster, or another tool
- CPU microcode package and loading path
- Root filesystem, subvolumes, encryption, and resume layout
- Secure Boot signing and key ownership, if enabled

## Preserve Recovery

Before a risky change:

- Keep at least one known-good kernel and boot entry.
- Confirm access to an Arch installation image and know the mount/chroot procedure
  for this exact filesystem and encryption layout.
- Back up irreplaceable data outside the affected disk.
- Save current boot and generator configuration.
- If remote, do not reboot without an out-of-band recovery path.

Do not provide generic `mount /dev/sdX...` recovery commands as if device names and
subvolumes were universal. Derive commands from `lsblk`, `findmnt`, crypt mappings,
and the actual layout.

## Kernels and Out-of-Tree Modules

Treat a kernel package, its headers, DKMS modules, initramfs artifacts, GPU modules,
and bootloader entries as one change. After kernel work, verify installed artifacts
and DKMS status for the target kernel before rebooting.

Do not remove the running or last known-good kernel until the replacement has booted
successfully and recovery remains available.

## Initramfs and Bootloader Commands

Use only the generator and bootloader already identified. Do not mix mkinitcpio,
dracut, GRUB, systemd-boot, Limine, or rEFInd instructions. Check the installed
version's ArchWiki and upstream documentation before generating or signing artifacts.

Inspect command output and file timestamps before rebooting. A successful generator
exit does not prove that the firmware or bootloader points at the generated file.

## Filesystems, Encryption, and Snapshots

Confirm mountpoints and subvolume boundaries before edits. A Btrfs snapshot may omit
the ESP, a separate home, databases with special handling, or data outside its
subvolume. LUKS changes require header-backup and recovery planning appropriate to
the actual device.

Never suggest partition resizing, filesystem repair, rollback, or destructive fsck
operations without exact device identification, backups, and an offline/recovery
plan where required.
