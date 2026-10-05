# kpsinghchouhan.linux_developer_roles.visual_studio_code

Installs [Visual Studio Code](https://code.visualstudio.com) on macOS.

## Supported platforms

| Platform | Package installation                             |
| -------- | ------------------------------------------------ |
| macOS    | `community.general.homebrew_cask` (runs as user) |

The role fails on any other platform.

The `visual-studio-code` cask installs `/Applications/Visual Studio Code.app` and links the `code`
and `code-tunnel` commands into the Homebrew `bin` directory. You do not need VS Code's
"Shell Command: Install 'code' command in PATH" step. The cask needs no sudo.

## Requirements

- `community.general` collection (declared as a collection dependency), for Homebrew.
- Homebrew already installed.
- Facts gathered for the play (`gather_facts: true`, the default). The role uses `os_family`.

## Role Variables

| Variable                           | Default     | Description                                                         |
| ---------------------------------- | ----------- | ------------------------------------------------------------------- |
| `visual_studio_code_package_state` | `"present"` | `present` or `latest`. Use `latest` to upgrade an existing install. |

All variables are validated by `meta/argument_specs.yml`.

## Updates

Visual Studio Code updates itself, so `present` is enough for most hosts. Homebrew marks the cask
as `auto_updates` and skips it when it checks for upgrades. With
`visual_studio_code_package_state: latest` the role passes `greedy: true`, so Homebrew upgrades
VS Code when its cask has a newer version.

## Extensions and settings

The role does not manage extensions or settings. Install extensions with
`code --install-extension <publisher.name>`, or turn on Settings Sync in VS Code.

## Dependencies

None.

## Example Playbook

```yaml
- name: Install developer tools
  hosts: workstations
  roles:
    - role: kpsinghchouhan.linux_developer_roles.visual_studio_code
      when: ansible_facts['os_family'] == 'Darwin'
```

## Role Idempotency

True. A second run with the same variables reports no changes, unless
`visual_studio_code_package_state: latest` and Homebrew has a newer version. Check mode is supported.

## Role Atomicity

True. The role installs one cask.

## Roll-back capabilities

None. To remove VS Code, run `brew uninstall --cask visual-studio-code`. Extensions in `~/.vscode`
and settings in `~/Library/Application Support/Code` are left in place.

## License

GPL-2.0-or-later

## Author Information

Krishana Pal Singh Chouhan
