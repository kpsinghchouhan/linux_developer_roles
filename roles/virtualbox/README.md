# kpsinghchouhan.linux_developer_roles.virtualbox

Installs [Oracle VirtualBox](https://www.virtualbox.org) on macOS.

## Supported platforms

| Platform | Package installation                             |
| -------- | ------------------------------------------------ |
| macOS    | `community.general.homebrew_cask` (runs as user) |

The role fails on any other platform.

The `virtualbox` cask installs `/Applications/VirtualBox.app` and the command-line tools, such as
`VBoxManage`, from a `.pkg` installer. Homebrew picks the build for the host: the Apple silicon
build runs ARM guests only, and the Intel build runs x86 guests.

## Requirements

- `community.general` collection (declared as a collection dependency), for Homebrew.
- Homebrew already installed.
- sudo rights for the connecting user. Homebrew runs the `.pkg` installer with `sudo`. Set
  `virtualbox_sudo_password` unless the user has passwordless sudo.
- Facts gathered for the play (`gather_facts: true`, the default). The role uses `os_family`.

The cask conflicts with the `virtualbox@6` and `virtualbox@beta` casks. Uninstall them first.

## Role Variables

| Variable                   | Default     | Description                                                         |
| -------------------------- | ----------- | ------------------------------------------------------------------- |
| `virtualbox_package_state` | `"present"` | `present` or `latest`. Use `latest` to upgrade an existing install. |
| `virtualbox_sudo_password` | not set     | Password for the `.pkg` installer's sudo prompt. Not logged.        |

All variables are validated by `meta/argument_specs.yml`.

## First launch

On Intel Macs, macOS may block the VirtualBox kernel extensions after the install. Allow
"Oracle America, Inc." in System Settings > Privacy & Security, then restart the Mac.

## Dependencies

None.

## Example Playbook

```yaml
- name: Install developer tools
  hosts: workstations
  roles:
    - role: kpsinghchouhan.linux_developer_roles.virtualbox
      virtualbox_sudo_password: "{{ ansible_become_password }}"
      when: ansible_facts['os_family'] == 'Darwin'
    - role: kpsinghchouhan.linux_developer_roles.hashicorp_vagrant
      hashicorp_vagrant_sudo_password: "{{ ansible_become_password }}"
      when: ansible_facts['os_family'] == 'Darwin'
```

Run the playbook with `--ask-become-pass`, or keep the password in Ansible Vault.

## Role Idempotency

True. A second run with the same variables reports no changes, unless
`virtualbox_package_state: latest` and a newer release is available. Check mode is supported.

## Role Atomicity

True. The role installs one cask.

## Roll-back capabilities

None. To remove VirtualBox, run `brew uninstall --cask virtualbox`. It also asks for sudo. Virtual
machines in `~/VirtualBox VMs` are left in place.

## License

GPL-2.0-or-later

## Author Information

Krishana Pal Singh Chouhan
