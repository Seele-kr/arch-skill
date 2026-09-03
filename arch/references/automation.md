# Automation: Hooks, Timers, and User Services

Read this before adding automation triggered by package transactions, login, boot,
file changes, timers, sleep, hardware events, or desktop-session events.

## Choose the Native Mechanism

- Repeating or calendar work: systemd timer
- Long-running or dependency-aware process: systemd service
- Per-user desktop/session work: systemd user unit
- Package transaction event: pacman hook
- Login-only environment: the session or shell mechanism actually used
- Application event: the application's supported hook interface

Avoid polling loops and duplicate autostart paths when a native event exists.

## systemd Automation

Put system units in `/etc/systemd/system` and user units in
`~/.config/systemd/user`. Read [systemd.md](systemd.md) before creating or enabling
them. Pair a timer with a service so the action is independently testable. Use
`systemd-analyze calendar` to validate calendar expressions.

Make scripts idempotent where repeated activation is possible. Define failure
behavior, timeouts, working directory, environment, and logging deliberately.

## Pacman Hooks

User-created hooks normally live under `/etc/pacman.d/hooks`; package-owned hooks are
under `/usr/share/libalpm/hooks`. Do not edit package-owned hooks.

Before adding a hook, read `alpm-hooks(5)` and inspect existing hooks. Keep triggers
narrow. Package hooks run in a privileged package transaction context, so do not run
untrusted repository code, depend on an interactive session, or write into a user's
home implicitly.

A hook failure can affect an upgrade. Prefer post-transaction work when ordering
allows it, keep actions deterministic, and provide a manual recovery command.

## Verification

Test the underlying command first, then the service or hook in the smallest safe
scope. Inspect its journal or pacman transaction output. Verify a timer with
`systemctl list-timers` or `systemctl --user list-timers` as appropriate.

Do not trigger a real package transaction, reboot, suspend, or destructive event
solely to test automation when a dry run or direct unit start provides meaningful
coverage.
