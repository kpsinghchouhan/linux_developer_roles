# kpsinghchouhan.linux_developer_roles.zed

Installs the [Zed](https://zed.dev) editor on macOS.

## Supported platforms

| Platform | Package installation                             |
| -------- | ------------------------------------------------ |
| macOS    | `community.general.homebrew_cask` (runs as user) |

The role fails on any other platform.

The `zed` cask installs `/Applications/Zed.app` and links the `zed` command into the Homebrew `bin`
directory, with shell completions. You do not need Zed's "Install CLI" step. The cask needs no sudo.

## Requirements

- `community.general` collection (declared as a collection dependency), for Homebrew.
- Homebrew already installed.
- Facts gathered for the play (`gather_facts: true`, the default). The role uses `os_family`.

## Role Variables

| Variable            | Default     | Description                                                         |
| ------------------- | ----------- | ------------------------------------------------------------------- |
| `zed_package_state` | `"present"` | `present` or `latest`. Use `latest` to upgrade an existing install. |

All variables are validated by `meta/argument_specs.yml`.

## Updates

Zed updates itself, so `present` is enough for most hosts. Homebrew marks the cask as
`auto_updates` and skips it when it checks for upgrades. With `zed_package_state: latest` the role
passes `greedy: true`, so Homebrew upgrades Zed when its cask has a newer version.

## Extensions and settings

The role does not manage extensions or settings. Zed keeps its settings in
`~/.config/zed/settings.json`.

## Dependencies

None.

## Example Playbook

```yaml
- name: Install developer tools
  hosts: workstations
  roles:
    - role: kpsinghchouhan.linux_developer_roles.zed
      when: ansible_facts['os_family'] == 'Darwin'
```

## Role Idempotency

True. A second run with the same variables reports no changes, unless `zed_package_state: latest`
and Homebrew has a newer version. Check mode is supported.

## Role Atomicity

True. The role installs one cask.

## Roll-back capabilities

None. To remove Zed, run `brew uninstall --cask zed`. Settings in `~/.config/zed` and data in
`~/Library/Application Support/Zed` are left in place.

## License

GPL-2.0-or-later

## Author Information

Krishana Pal Singh Chouhan
