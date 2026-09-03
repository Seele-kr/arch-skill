# Hyprland Configuration

Read this before changing Hyprland keybindings, monitors, window or workspace rules,
input, animations, autostart, environment, or compositor behavior on Arch.

## Discover the Configuration Graph

Vanilla Arch does not define a standard dotfile layout beyond Hyprland's supported
configuration entrypoint. Inspect the user's files and `source` directives instead
of assuming Omarchy's Lua files or another distribution's layout:

```bash
hyprctl version
hyprctl systeminfo
find ~/.config/hypr -maxdepth 3 -type f -print 2>/dev/null | sort
rg '^\s*source\s*=' ~/.config/hypr 2>/dev/null
```

Preserve an existing generated, templated, or dotfile-managed workflow. If a file
says it is generated, find its source rather than editing generated output.

## Current Syntax Is Required

Hyprland syntax changes frequently. Before writing window rules, workspace rules,
bindings, monitor directives, or plugin configuration, check the matching page in
the official [Hyprland Wiki](https://wiki.hypr.land/) for the installed version.
Do not translate Omarchy Lua helpers such as `o.bind`, `hl.monitor`, or `o.window`
into a vanilla system as if they were native Hyprland syntax.

## Keybindings

Inspect effective bindings and the files that define them:

```bash
hyprctl binds
rg '^\s*bind' ~/.config/hypr 2>/dev/null
```

Before rebinding a key, identify its current action and whether it comes from a
sourced file. Remove or override it according to current Hyprland semantics, and tell
the user what behavior is being replaced.

## Monitors

Inspect names, modes, scale, and current placement:

```bash
hyprctl monitors all
```

Do not guess connector names. Keep a fallback configuration when changing the only
display or when a remote session could become inaccessible. Account for logical
coordinates after scaling, not only raw pixel dimensions.

## Window and Workspace Rules

Read the current official rule documentation before every rule edit. Inspect live
window properties with commands supported by the installed Hyprland version, then
match the narrowest stable property. Avoid broad regexes that affect unrelated apps.

## Autostart and Session Environment

Identify whether applications start through Hyprland `exec-once`, a display manager,
systemd user units, UWSM, XDG autostart, or a dotfile framework. Add the change to the
existing mechanism rather than creating a second competing startup path.

For portals, screen sharing, notifications, PipeWire, or environment propagation,
inspect the systemd user session and relevant current-boot user journal. These are
often session integration problems, not compositor package problems.

## Apply and Validate

After changing active Hyprland configuration:

```bash
hyprctl reload
hyprctl configerrors
```

Resolve reported errors and rerun both commands. Then exercise the actual binding,
rule, monitor layout, or workspace behavior. Some components such as portals,
idle daemons, wallpaper daemons, and lock screens are separate processes and require
their own reload or restart; `hyprctl reload` does not validate their configs.
