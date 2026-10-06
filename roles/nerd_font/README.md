# kpsinghchouhan.linux_developer_roles.nerd_font

Installs [Nerd Fonts](https://www.nerdfonts.com/) on macOS. Nerd Fonts add icons and symbols
used by prompts such as [starship](https://starship.rs).

## Supported platforms

| Platform | Package installation                             |
| -------- | ------------------------------------------------ |
| macOS    | `community.general.homebrew_cask` (runs as user) |

The role fails on any other platform.

Homebrew installs each font to `~/Library/Fonts`.

## Requirements

- `community.general` collection (declared as a collection dependency), for Homebrew.
- Homebrew already installed.
- Facts gathered for the play (`gather_facts: true`, the default). The role uses `os_family`.

## Role Variables

| Variable                  | Default         | Description                                                                 |
| ------------------------- | --------------- | --------------------------------------------------------------------------- |
| `nerd_font_fonts`         | `["fira-code"]` | Fonts to install, by cask name without `font-` and `-nerd-font`.            |
| `nerd_font_package_state` | `"present"`     | `present` or `latest`. Use `latest` to upgrade existing fonts.              |

All variables are validated by `meta/argument_specs.yml`.

Each entry in `nerd_font_fonts` installs the cask `font-<entry>-nerd-font`. For example:

| Entry            | Cask                            | Font name in apps         |
| ---------------- | ------------------------------- | ------------------------- |
| `fira-code`      | `font-fira-code-nerd-font`      | FiraCode Nerd Font        |
| `jetbrains-mono` | `font-jetbrains-mono-nerd-font` | JetBrainsMono Nerd Font   |
| `meslo-lg`       | `font-meslo-lg-nerd-font`       | MesloLGS Nerd Font        |
| `hack`           | `font-hack-nerd-font`           | Hack Nerd Font            |

Run `brew search nerd-font` to list all fonts.

## Dependencies

None.

## Example Playbook

```yaml
- name: Install developer tools
  hosts: workstations
  roles:
    - role: kpsinghchouhan.linux_developer_roles.nerd_font
      nerd_font_fonts:
        - fira-code
        - jetbrains-mono
      when: ansible_facts['os_family'] == 'Darwin'
    - role: kpsinghchouhan.linux_developer_roles.starship
```

The role does not change terminal settings. Select the font in your terminal's preferences after
the role runs.

## Role Idempotency

True. A second run with the same variables reports no changes, unless
`nerd_font_package_state: latest` and a newer release is available. Check mode is supported.

## Role Atomicity

False. Each font is a separate cask install. A failed run can leave some fonts installed;
re-running the role completes it.

## Roll-back capabilities

None. To remove a font, run `brew uninstall --cask font-<name>-nerd-font`.

## License

GPL-2.0-or-later

## Author Information

Krishana Pal Singh Chouhan
