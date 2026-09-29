# kpsinghchouhan.linux_developer_roles.claude

Installs [Claude Code](https://code.claude.com/docs/en/setup) (`claude`) on Ubuntu and macOS, and
[Claude Desktop](https://claude.ai/download) on macOS.

## Supported platforms

| Platform | Claude Code                                                                | Claude Desktop                                   |
| -------- | -------------------------------------------------------------------------- | ------------------------------------------------ |
| Ubuntu   | `ansible.builtin.apt` from the Claude Code apt repository (needs `become`) | Not installed                                    |
| macOS    | `community.general.homebrew_cask` (runs as user)                           | `community.general.homebrew_cask` (runs as user) |

On Ubuntu the role adds Anthropic's release signing key to `/etc/apt/keyrings` and the
repository to `/etc/apt/sources.list.d/claude-code.list`, as documented by Anthropic.

## Requirements

- `community.general` collection (declared as a collection dependency), for Homebrew on macOS.
- Homebrew already installed on macOS hosts.
- Privilege escalation on Ubuntu for the repository and package install.
- Internet access to `downloads.claude.ai` on Ubuntu hosts.
- Facts gathered for the play (`gather_facts: true`, the default). The role uses `os_family`.

## Role Variables

| Variable                       | Default                                          | Description                                                                         |
| ------------------------------ | ------------------------------------------------ | ----------------------------------------------------------------------------------- |
| `claude_code_channel`          | `"stable"`                                       | `stable` or `latest`. Selects the apt repository on Ubuntu and the cask on macOS.   |
| `claude_code_package_state`    | `"present"`                                      | `present` or `latest`. Use `latest` to upgrade an existing Claude Code.             |
| `claude_code_apt_keyring_url`  | `"https://downloads.claude.ai/keys/claude-code.asc"` | URL of the release signing key. Ubuntu only.                                    |
| `claude_code_apt_keyring_path` | `"/etc/apt/keyrings/claude-code.asc"`            | Where the signing key is saved. Ubuntu only.                                        |
| `claude_code_apt_repo_url`     | `"https://downloads.claude.ai/claude-code/apt"`  | Base URL of the apt repository; the channel is appended. Ubuntu only.               |
| `claude_desktop_install`       | `true`                                           | Install Claude Desktop. macOS only.                                                 |
| `claude_desktop_cask_name`     | `"claude"`                                       | Name of the Claude Desktop cask. macOS only.                                        |
| `claude_desktop_package_state` | `"present"`                                      | `present` or `latest`. Use `latest` to upgrade an existing Claude Desktop. macOS only. |

All variables are validated by `meta/argument_specs.yml`.

On macOS, `claude_code_channel: stable` installs the `claude-code` cask and `latest` installs
`claude-code@latest`. Installs from apt and Homebrew do not auto-update. Set
`claude_code_package_state: latest` to upgrade on each run.

## Dependencies

None.

## Example Playbook

```yaml
- name: Install developer tools
  hosts: workstations
  roles:
    - role: kpsinghchouhan.linux_developer_roles.gh
    - role: kpsinghchouhan.linux_developer_roles.git
      git_user_name: "Jane Doe"
      git_user_email: "jane@example.com"
    - role: kpsinghchouhan.linux_developer_roles.claude
      claude_code_channel: latest
```

The role does not log in to Claude. Run `claude` once on the host and follow the browser prompts.

## Role Idempotency

True. A second run with the same variables reports no changes, unless a package state is
`latest` and a newer release is available. Check mode is supported.

## Role Atomicity

False. On Ubuntu the key, repository and package are separate steps. A failed run can
leave the repository configured without the package installed; re-running the role completes it.

## Roll-back capabilities

None. To remove the install on Ubuntu, remove the `claude-code` package,
`/etc/apt/sources.list.d/claude-code.list` and the key file. On macOS, run
`brew uninstall --cask claude-code claude` (or `claude-code@latest` for the latest channel).

## License

GPL-2.0-or-later

## Author Information

Krishana Pal Singh Chouhan
