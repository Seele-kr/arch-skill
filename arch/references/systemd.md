# systemd Services and Journals

Read this before changing services, timers, sockets, boot targets, user-session
units, or persistent service configuration.

## Choose the Correct Manager

System units and user units are separate:

```bash
systemctl status unit_name
systemctl --user status unit_name
```

Do not add `--user` merely to avoid privilege. A desktop application, portal,
PipeWire, or per-user daemon commonly uses the user manager; hardware, networking,
storage, and system daemons commonly use the system manager. Confirm with the unit's
actual installation location and status.

## Diagnose First

```bash
systemctl --failed
systemctl status unit_name
systemctl cat unit_name
systemctl show unit_name
journalctl -u unit_name -b
```

User unit equivalents:

```bash
systemctl --user --failed
systemctl --user status unit_name
systemctl --user cat unit_name
journalctl --user -u unit_name -b
```

Preserve the first meaningful error. A later dependency failure or restart-loop
message may only be a consequence.

## Persistent Changes

Never edit vendor units in `/usr/lib/systemd/system` or
`/usr/lib/systemd/user`. Use a drop-in:

```bash
sudo systemctl edit unit_name
systemctl --user edit unit_name
```

Inspect the merged result with `systemctl cat`. Use a full replacement unit only
when a drop-in cannot express the required change, and document why.

After changing unit files directly under `/etc/systemd/system` or
`~/.config/systemd/user`, reload the appropriate manager:

```bash
sudo systemctl daemon-reload
systemctl --user daemon-reload
```

`enable` controls future activation through install links; `start` controls the
current runtime. Do not conflate them. Before enabling a unit, inspect its `[Install]`
section and whether socket, timer, D-Bus, path, or dependency activation is intended.

## Editing Environment and Commands

systemd does not evaluate shell syntax in `ExecStart=` unless a shell is explicitly
invoked. Prefer direct executable paths and systemd specifiers/environment directives.
If a shell is genuinely needed, make quoting and failure behavior explicit.

For user-session environment issues, inspect how the display manager or compositor
imports variables. A shell startup file may not affect graphical user services.

## Verification

```bash
systemd-analyze verify /path/to/unit
systemctl status unit_name
journalctl -u unit_name -b --no-pager
```

For user units, pass `--user` to `systemctl` and `journalctl`. Verify the behavior
that the service provides, not merely that its process stayed active.

Do not reboot just to test an ordinary unit change when start/restart and journal
inspection can validate it safely.
