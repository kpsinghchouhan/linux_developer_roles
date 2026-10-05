# kpsinghchouhan.linux_developer_roles.google_chrome

Installs [Google Chrome](https://www.google.com/chrome/) on macOS.

## Supported platforms

| Platform | Package installation                             |
| -------- | ------------------------------------------------ |
| macOS    | `community.general.homebrew_cask` (runs as user) |

The role fails on any other platform.

The `google-chrome` cask installs `/Applications/Google Chrome.app`. It needs no sudo.

## Requirements

- `community.general` collection (declared as a collection dependency), for Homebrew.
- Homebrew already installed.
- Facts gathered for the play (`gather_facts: true`, the default). The role uses `os_family`.

## Role Variables

| Variable                      | Default     | Description                                                         |
| ----------------------------- | ----------- | ------------------------------------------------------------------- |
| `google_chrome_package_state` | `"present"` | `present` or `latest`. Use `latest` to upgrade an existing install. |

All variables are validated by `meta/argument_specs.yml`.

## Updates

Chrome updates itself, so `present` is enough for most hosts. Homebrew marks the cask as
`auto_updates` and skips it when it checks for upgrades. With `google_chrome_package_state: latest`
the role passes `greedy: true`, so Homebrew upgrades Chrome when its cask has a newer version.

## Dependencies

None.

## Example Playbook

```yaml
- name: Install desktop apps
  hosts: workstations
  roles:
    - role: kpsinghchouhan.linux_developer_roles.google_chrome
      when: ansible_facts['os_family'] == 'Darwin'
```

## Role Idempotency

True. A second run with the same variables reports no changes, unless
`google_chrome_package_state: latest` and Homebrew has a newer version. Check mode is supported.

## Role Atomicity

True. The role installs one cask.

## Roll-back capabilities

None. To remove Chrome, run `brew uninstall --cask google-chrome`.

## License

GPL-2.0-or-later

## Author Information

Krishana Pal Singh Chouhan
