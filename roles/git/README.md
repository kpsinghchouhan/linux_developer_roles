# kpsinghchouhan.linux_developer_roles.git

Installs git and manages a user's `~/.gitconfig`.

The whole `.gitconfig` is rendered from a template, so the role owns the file:
settings added by hand are overwritten on the next run (a backup is kept by default).

## Supported platforms

| Platform | Package installation                         |
| -------- | -------------------------------------------- |
| Ubuntu   | `ansible.builtin.apt` (needs `become`)       |
| macOS    | `community.general.homebrew` (runs as user) |

## Requirements

- `community.general` collection (declared as a collection dependency), for Homebrew on macOS.
- Homebrew already installed on macOS hosts.
- Privilege escalation on Ubuntu for the apt install, and on any platform when
  `git_config_owner` is not the connecting user.

## Role Variables

| Variable                  | Default  | Description                                                              |
| ------------------------- | -------- | ------------------------------------------------------------------------ |
| `git_install_package`     | `true`   | Install the git package.                                                 |
| `git_config_owner`        | `""`     | User whose `.gitconfig` is managed. Empty means the connecting user.     |
| `git_config_home`         | `""`     | Directory in which `.gitconfig` is written. Empty means the owner's home. |
| `git_config_backup`       | `true`   | Keep a timestamped backup of `.gitconfig` when it changes.               |
| `git_config_mode`         | `"0644"` | File mode of `.gitconfig`.                                               |
| `git_user_name`           | `""`     | `user.name`. Omitted when empty.                                         |
| `git_user_email`          | `""`     | `user.email`. Omitted when empty.                                        |
| `git_core_editor`         | `"vim"`  | `core.editor`.                                                           |
| `git_init_default_branch` | `"main"` | `init.defaultBranch`.                                                    |
| `git_pull_rebase`         | `false`  | `pull.rebase`.                                                           |
| `git_aliases`             | `{}`     | Entries of the `[alias]` section, keyed by alias name.                   |
| `git_config_extra`        | `{}`     | Additional sections: `{section: {option: value}}`.                       |

Subsections in `git_config_extra` are written as the section name, for example
`'diff "sopsdiffer"'`. Boolean values are rendered as `true`/`false`.

All variables are validated by `meta/argument_specs.yml`.

## Dependencies

None.

## Example Playbook

```yaml
- name: Configure git for developers
  hosts: workstations
  roles:
    - role: kpsinghchouhan.linux_developer_roles.git
      git_user_name: "Jane Doe"
      git_user_email: "jane@example.com"
      git_aliases:
        co: checkout
        st: status
      git_config_extra:
        push:
          autoSetupRemote: true
```

Manage the `.gitconfig` of another user (requires `become`):

```yaml
- name: Configure git for a service account
  hosts: servers
  tasks:
    - name: Configure git for the deploy user
      ansible.builtin.include_role:
        name: kpsinghchouhan.linux_developer_roles.git
      vars:
        git_install_package: false
        git_config_owner: deploy
        git_user_name: "Deploy Bot"
        git_user_email: "deploy@example.com"
```

The role works with `gather_facts: false`; it gathers the minimal facts it needs.

## Role Idempotency

True. A second run with the same variables reports no changes. Check mode is supported.

## Role Atomicity

True. `.gitconfig` is written atomically by `ansible.builtin.template`.

## Roll-back capabilities

With `git_config_backup: true`, the previous file is kept next to it as
`.gitconfig.<pid>.<timestamp>~` and can be restored by hand. The git package is not removed.

## License

GPL-2.0-or-later

## Author Information

Krishana Pal Singh Chouhan
