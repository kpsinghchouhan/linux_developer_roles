# kpsinghchouhan.linux_developer_roles.google_drive

Installs [Google Drive for desktop](https://www.google.com/drive/download/) on macOS.

## Supported platforms

| Platform | Package installation                             |
| -------- | ------------------------------------------------ |
| macOS    | `community.general.homebrew_cask` (runs as user) |

The role fails on any other platform.

The `google-drive` cask installs `/Applications/Google Drive.app` from a `.pkg` installer.

## Requirements

- `community.general` collection (declared as a collection dependency), for Homebrew.
- Homebrew already installed.
- sudo rights for the connecting user. Homebrew runs the `.pkg` installer with `sudo`. Set
  `google_drive_sudo_password` unless the user has passwordless sudo.
- Facts gathered for the play (`gather_facts: true`, the default). The role uses `os_family`.

## Role Variables

| Variable                     | Default     | Description                                                         |
| ---------------------------- | ----------- | ------------------------------------------------------------------- |
| `google_drive_package_state` | `"present"` | `present` or `latest`. Use `latest` to upgrade an existing install. |
| `google_drive_sudo_password` | not set     | Password for the `.pkg` installer's sudo prompt. Not logged.        |

All variables are validated by `meta/argument_specs.yml`.

## Updates

Google Drive updates itself, so `present` is enough for most hosts. Homebrew marks the cask as
`auto_updates` and skips it when it checks for upgrades. With `google_drive_package_state: latest`
the role passes `greedy: true`, so Homebrew upgrades Google Drive when its cask has a newer version.

## First launch

The role does not sign in. Open Google Drive, sign in with your Google account and, if macOS asks,
allow the Google Drive extension in System Settings.

## Dependencies

None.

## Example Playbook

```yaml
- name: Install desktop apps
  hosts: workstations
  roles:
    - role: kpsinghchouhan.linux_developer_roles.google_drive
      google_drive_sudo_password: "{{ ansible_become_password }}"
      when: ansible_facts['os_family'] == 'Darwin'
```

Run the playbook with `--ask-become-pass`, or keep the password in Ansible Vault.

## Role Idempotency

True. A second run with the same variables reports no changes, unless
`google_drive_package_state: latest` and Homebrew has a newer version. Check mode is supported.

## Role Atomicity

True. The role installs one cask.

## Roll-back capabilities

None. To remove Google Drive, run `brew uninstall --cask google-drive`. It also asks for sudo.

## License

GPL-2.0-or-later

## Author Information

Krishana Pal Singh Chouhan
