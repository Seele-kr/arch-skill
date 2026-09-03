# Arch Linux Agent Skill

An agent skill for administering and customizing installed Arch Linux systems.
It covers package and AUR workflows, systemd, boot and storage safety, Hyprland,
desktop components, and event automation without assuming a particular installer
or dotfiles layout.

The skill is a vanilla-Arch adaptation of Omarchy's end-user agent skill. It keeps
the original's evidence-first workflow and user/package configuration boundary,
while removing Omarchy-specific CLI commands, paths, Lua helpers, and Quickshell
components.

## Install

For Codex, link the checkout into the user skill directory:

```bash
mkdir -p ~/.codex/skills
ln -s ~/src/github.com/Seele-kr/arch-skill/arch ~/.codex/skills/arch
```

Codex discovers the skill automatically. Invoke it explicitly as `$arch`, or ask an
Arch Linux administration or customization question that matches its description.

Agents that use the shared Agent Skills location can link the same directory under
`~/.agents/skills/arch` instead.

## Layout

```text
arch/
├── SKILL.md
├── agents/openai.yaml
└── references/
    ├── automation.md
    ├── boot-and-storage.md
    ├── desktop.md
    ├── hyprland.md
    ├── packages.md
    └── systemd.md
```

## Attribution

Derived from the [Omarchy agent skill](https://github.com/omacom/omarchy/tree/quattro/default/agents/skills/omarchy),
originally by David Heinemeier Hansson and distributed under the MIT License.

## License

MIT
