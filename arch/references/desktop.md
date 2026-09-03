# Desktop and Wayland Components

Read this for terminal configuration, themes, fonts, bars, launchers, notifications,
portals, audio, idle/lock behavior, screenshots, recording, or file sharing.

## Discover, Do Not Assume a Desktop Stack

Arch does not ship Omarchy's unified theme, shell, or command center. Identify the
installed and running components:

```bash
printf 'session=%s desktop=%s\n' "$XDG_SESSION_TYPE" "$XDG_CURRENT_DESKTOP"
systemctl --user --failed
systemctl --user status pipewire pipewire-pulse wireplumber 2>&1 || true
systemctl --user status xdg-desktop-portal 2>&1 || true
pacman -Q | rg '(hypr|waybar|quickshell|wofi|rofi|walker|mako|dunst|pipewire|portal)'
```

Inspect the application-specific file already in use. Common locations are only
leads, not guarantees:

```text
~/.config/alacritty/alacritty.toml
~/.config/foot/foot.ini
~/.config/kitty/kitty.conf
~/.config/ghostty/config
~/.config/waybar/
~/.config/quickshell/
~/.config/mako/
```

Respect dotfile managers, generated files, includes, and symlinks. Edit their source
of truth instead of breaking the management workflow.

## Themes and Fonts

There is no universal Arch theme transaction. Determine which apps consume GTK,
Qt, terminal, editor, icon, cursor, wallpaper, or compositor settings. Change only
the layers requested and explain when a consistent appearance requires multiple
independent configurations.

Do not edit package-owned themes under `/usr/share`. Copy or extend them through the
consumer's supported user-level mechanism, and preserve the original license when
redistributing a derived theme.

After installing fonts, confirm the fontconfig-visible name instead of guessing from
the package name:

```bash
fc-list | rg -i 'font name'
fc-match 'Font Family'
```

## Portals and Screen Sharing

Wayland screen sharing crosses the compositor, the matching portal backend,
`xdg-desktop-portal`, PipeWire, and the application. Inspect user units and user
journals before reinstalling packages. Avoid running multiple incompatible portal
backends for the same desktop without an explicit selection configuration.

## Audio and Bluetooth

Distinguish PipeWire's media services, WirePlumber policy, PulseAudio compatibility,
ALSA devices, and BlueZ pairing/profile state. Use `wpctl status`, relevant user-unit
status, and the current-boot user journal. Do not replace the audio stack as a first
diagnostic step.

## Capture Tools

Discover installed tools before choosing commands:

```bash
command -v grim slurp hyprshot wayshot spectacle wf-recorder 2>/dev/null
```

Typical Wayland components include a compositor-specific screenshot tool or
`grim`/`slurp`, and `wf-recorder` for recording, but flags and portal behavior change.
Read current tool help before executing. Ask for the intended region, destination,
clipboard behavior, audio sources, and recording stop mechanism when they matter.

Captures can contain secrets, notifications, account data, or other people. Do not
upload or share them without explicit authorization.

## Apply and Verify

Use the component's own reload, parser, signal, or user service. A compositor reload
does not reload Waybar, Quickshell, a wallpaper daemon, an idle daemon, a portal, or
a terminal. Validate the component that owns the edited configuration.
