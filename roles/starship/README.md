# kpsinghchouhan.linux_developer_roles.starship

Installs the [starship](https://starship.rs) prompt, copies its config file to
`~/.config/starship.toml` and enables it in bash and zsh.

## Supported platforms

| Platform | Package installation                                                                       |
| -------- | ------------------------------------------------------------------------------------------ |
| Ubuntu   | `ansible.builtin.unarchive` of the GitHub release to `/usr/local/bin` (needs `become`)     |
| macOS    | `community.general.homebrew` (runs as user)                                                |

On Ubuntu the role installs the static `musl` build for the host architecture (`x86_64` or
`aarch64`). Ubuntu 24.04 and older have no starship package.

## Requirements

- `community.general` collection (declared as a collection dependency), for Homebrew on macOS.
- Homebrew already installed on macOS hosts.
- Privilege escalation on Ubuntu for the binary install.
- Internet access to `github.com` on Ubuntu hosts.
- Facts gathered for the play (`gather_facts: true`, the default). The role uses `os_family`,
  `architecture`, `user_id`, `user_gid` and `env`.
- A [Nerd Font](https://www.nerdfonts.com/) in the terminal if your config uses Nerd Font symbols.
  The default config does not.

## Role Variables

| Variable                 | Default                         | Description                                                                         |
| ------------------------ | ------------------------------- | ----------------------------------------------------------------------------------- |
| `starship_version`       | `"latest"`                      | Release to install, such as `"1.23.0"`. Ubuntu only.                                |
| `starship_install_dir`   | `"/usr/local/bin"`              | Directory for the starship binary. Ubuntu only.                                     |
| `starship_package_state` | `"present"`                     | `present` or `latest`. Use `latest` to upgrade an existing starship. macOS only.    |
| `starship_config_src`    | `"starship.toml"`               | Source of `starship.toml`: a file in the role's `files/`, or a path on the controller. |
| `starship_config_home`   | `ansible_facts['env']['HOME']`  | Home directory that gets `.config/starship.toml` and the rc files.                  |
| `starship_config_mode`   | `"0644"`                        | File mode of `starship.toml`.                                                       |
| `starship_shells`        | `["bash", "zsh"]`               | Shells whose rc file gets `eval "$(starship init <shell>)"`. Use `[]` to skip.      |

All variables are validated by `meta/argument_specs.yml`.

With `starship_version: latest`, an existing starship binary is left in place. Set a version
number to upgrade or downgrade on Ubuntu.

## Config file

The role owns `~/.config/starship.toml` and replaces it on every run. The default is a short config
in [files/starship.toml](files/starship.toml). To use your own, keep it next to your playbook:

```yaml
starship_config_src: "{{ playbook_dir }}/files/starship.toml"
```

The role adds one line to `~/.bashrc` and `~/.zshrc`, and creates each file if it is missing.
The rest of each file is left alone.

## Dependencies

None.

## Example Playbook

```yaml
- name: Install developer tools
  hosts: workstations
  roles:
    - role: kpsinghchouhan.linux_developer_roles.gh
    - role: kpsinghchouhan.linux_developer_roles.starship
      starship_config_src: "{{ playbook_dir }}/files/starship.toml"
      starship_shells:
        - zsh
```

Open a new shell after the role runs to see the prompt.

## Role Idempotency

True. A second run with the same variables reports no changes, unless
`starship_package_state: latest` and a newer release is available on macOS. Check mode is supported.

## Role Atomicity

False. The binary, config file and rc files are separate steps. A failed run can leave some of
them in place; re-running the role completes it.

## Roll-back capabilities

None. To remove starship on Ubuntu, delete `/usr/local/bin/starship`. On macOS, run
`brew uninstall starship`. Then delete `~/.config/starship.toml` and the `starship init` lines in
`~/.bashrc` and `~/.zshrc`.

## License

GPL-2.0-or-later

## Author Information

Krishana Pal Singh Chouhan
