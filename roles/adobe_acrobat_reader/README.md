# kpsinghchouhan.linux_developer_roles.adobe_acrobat_reader

Installs [Adobe Acrobat Reader](https://www.adobe.com/acrobat/pdf-reader.html) on macOS.

## Supported platforms

| Platform | Package installation                             |
| -------- | ------------------------------------------------ |
| macOS    | `community.general.homebrew_cask` (runs as user) |

The role fails on any other platform.

The `adobe-acrobat-reader` cask installs `/Applications/Adobe Acrobat Reader.app` and Adobe's
updater from a `.pkg` installer. The cask needs macOS 13 or later.

## Requirements

- `community.general` collection (declared as a collection dependency), for Homebrew.
- Homebrew already installed.
- sudo rights for the connecting user. Homebrew runs the `.pkg` installer with `sudo`. Set
  `adobe_acrobat_reader_sudo_password` unless the user has passwordless sudo.
- Facts gathered for the play (`gather_facts: true`, the default). The role uses `os_family`.

## Role Variables

| Variable                             | Default     | Description                                                       |
| ------------------------------------ | ----------- | ----------------------------------------------------------------- |
| `adobe_acrobat_reader_package_state` | `"present"` | `present` or `latest`. Use `latest` to upgrade an existing install. |
| `adobe_acrobat_reader_sudo_password` | not set     | Password for the `.pkg` installer's sudo prompt. Not logged.      |

All variables are validated by `meta/argument_specs.yml`.

## Dependencies

None.

## Example Playbook

```yaml
- name: Install desktop apps
  hosts: workstations
  roles:
    - role: kpsinghchouhan.linux_developer_roles.adobe_acrobat_reader
      adobe_acrobat_reader_sudo_password: "{{ ansible_become_password }}"
      when: ansible_facts['os_family'] == 'Darwin'
```

Run the playbook with `--ask-become-pass`, or keep the password in Ansible Vault.

## Role Idempotency

True. A second run with the same variables reports no changes, unless
`adobe_acrobat_reader_package_state: latest` and a newer release is available. Check mode is supported.

## Role Atomicity

True. The role installs one cask.

## Roll-back capabilities

None. To remove Acrobat Reader, run `brew uninstall --cask adobe-acrobat-reader`.

## License

GPL-2.0-or-later

## Author Information

Krishana Pal Singh Chouhan
