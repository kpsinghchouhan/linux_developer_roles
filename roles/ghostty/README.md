# kpsinghchouhan.linux_developer_roles.ghostty

Installs the [Ghostty](https://ghostty.org) terminal on macOS and copies its config file to
`~/.config/ghostty/config`.

## Supported platforms

| Platform | Package installation                             |
| -------- | ------------------------------------------------ |
| macOS    | `community.general.homebrew_cask` (runs as user) |

The role fails on any other platform.

Homebrew installs the app to `/Applications/Ghostty.app`.

## Requirements

- `community.general` collection (declared as a collection dependency), for Homebrew.
- Homebrew already installed.
- Facts gathered for the play (`gather_facts: true`, the default). The role uses `os_family`,
  `user_id`, `user_gid` and `env`.

## Role Variables

| Variable                | Default                        | Description                                                                     |
| ----------------------- | ------------------------------ | ------------------------------------------------------------------------------- |
| `ghostty_package_state` | `"present"`                    | `present` or `latest`. Use `latest` to upgrade an existing Ghostty.             |
| `ghostty_config_src`    | `"config"`                     | Source of the config file: a file in the role's `files/`, or a path on the controller. |
| `ghostty_config_home`   | `ansible_facts['env']['HOME']` | Home directory that gets `.config/ghostty/config`.                              |
| `ghostty_config_mode`   | `"0644"`                       | File mode of the config file.                                                   |

All variables are validated by `meta/argument_specs.yml`.

## Config file

The role owns `~/.config/ghostty/config` and replaces it on every run. The default is a short config
in [files/config](files/config). It sets no font or theme, so Ghostty uses its built-in font
(JetBrains Mono with Nerd Font symbols). To use your own config, keep it next to your playbook:

```yaml
ghostty_config_src: "{{ playbook_dir }}/files/ghostty/config"
```

Ghostty does not reload the file on its own. After the role runs, press `Cmd+Shift+,` in Ghostty or
open a new window. Run this command to check a config file:

```sh
/Applications/Ghostty.app/Contents/MacOS/ghostty +validate-config --config-file=<path>
```

To use a Nerd Font installed by the `nerd_font` role, set it in your own config:

```text
font-family = "FiraCode Nerd Font"
```

## Dependencies

None.

## Example Playbook

```yaml
- name: Install developer tools
  hosts: workstations
  roles:
    - role: kpsinghchouhan.linux_developer_roles.nerd_font
      when: ansible_facts['os_family'] == 'Darwin'
    - role: kpsinghchouhan.linux_developer_roles.ghostty
      ghostty_config_src: "{{ playbook_dir }}/files/ghostty/config"
      when: ansible_facts['os_family'] == 'Darwin'
```

## Role Idempotency

True. A second run with the same variables reports no changes, unless
`ghostty_package_state: latest` and a newer release is available. Check mode is supported.

## Role Atomicity

False. The app and the config file are separate steps. A failed run can leave the app installed
without the config file; re-running the role completes it.

## Roll-back capabilities

None. To remove Ghostty, run `brew uninstall --cask ghostty`, then delete `~/.config/ghostty`.

## License

GPL-2.0-or-later

## Author Information

Krishana Pal Singh Chouhan
