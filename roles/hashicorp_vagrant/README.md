# kpsinghchouhan.linux_developer_roles.hashicorp_vagrant

Installs [HashiCorp Vagrant](https://developer.hashicorp.com/vagrant) on macOS.

## Supported platforms

| Platform | Package installation                             |
| -------- | ------------------------------------------------ |
| macOS    | `community.general.homebrew_cask` (runs as user) |

The role fails on any other platform.

The `vagrant` cask installs Vagrant to `/opt/vagrant` from a `.pkg` installer and links the
`vagrant` command into `/usr/local/bin`. It also adds bash and zsh completions under the Homebrew
prefix.

## Requirements

- `community.general` collection (declared as a collection dependency), for Homebrew.
- Homebrew already installed.
- sudo rights for the connecting user. Homebrew runs the `.pkg` installer with `sudo`. Set
  `hashicorp_vagrant_sudo_password` unless the user has passwordless sudo.
- Facts gathered for the play (`gather_facts: true`, the default). The role uses `os_family`.

Vagrant needs a provider, such as VirtualBox, VMware Fusion or Parallels, to run boxes. The role
does not install one.

## Role Variables

| Variable                          | Default     | Description                                                         |
| --------------------------------- | ----------- | ------------------------------------------------------------------- |
| `hashicorp_vagrant_package_state` | `"present"` | `present` or `latest`. Use `latest` to upgrade an existing install. |
| `hashicorp_vagrant_sudo_password` | not set     | Password for the `.pkg` installer's sudo prompt. Not logged.        |

All variables are validated by `meta/argument_specs.yml`.

## Dependencies

None.

## Example Playbook

```yaml
- name: Install developer tools
  hosts: workstations
  roles:
    - role: kpsinghchouhan.linux_developer_roles.hashicorp_vagrant
      hashicorp_vagrant_sudo_password: "{{ ansible_become_password }}"
      when: ansible_facts['os_family'] == 'Darwin'
```

Run the playbook with `--ask-become-pass`, or keep the password in Ansible Vault.

## Role Idempotency

True. A second run with the same variables reports no changes, unless
`hashicorp_vagrant_package_state: latest` and a newer release is available. Check mode is supported.

## Role Atomicity

True. The role installs one cask.

## Roll-back capabilities

None. To remove Vagrant, run `brew uninstall --cask vagrant`. It also asks for sudo. Boxes and
plugins in `~/.vagrant.d` are left in place.

## License

GPL-2.0-or-later

## Author Information

Krishana Pal Singh Chouhan
