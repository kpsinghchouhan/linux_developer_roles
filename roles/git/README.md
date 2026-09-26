# kpsinghchouhan.linux_developer_roles.git

Installs git and manages the connecting user's `~/.gitconfig`.

The whole `.gitconfig` is rendered from a template, so the role owns the file:
settings added by hand are overwritten on the next run.

## Supported platforms

| Platform | Package installation                                                       |
| -------- | -------------------------------------------------------------------------- |
| Ubuntu   | `ansible.builtin.package` (needs `become`)                                 |
| macOS    | `community.general.homebrew` (runs as user, skipped if `git_bin_path` exists) |

## Requirements

- `community.general` collection (declared as a collection dependency), for Homebrew on macOS.
- Homebrew already installed on macOS hosts.
- Privilege escalation on Ubuntu for the package install.
- Facts gathered for the play (`gather_facts: true`, the default). The role uses
  `os_family`, `user_id`, `user_gid` and `env.HOME`.
- The GitHub CLI (`gh`) on the host for the default `git_credential_helper` to work.

## Role Variables

| Variable                  | Default                     | Description                                                                 |
| ------------------------- | --------------------------- | --------------------------------------------------------------------------- |
| `git_bin_path`            | `"/usr/bin/git"`            | Path to the git binary. On macOS, the Homebrew install is skipped if it exists. |
| `git_package_name`        | `"git"`                     | Name of the git package to install.                                         |
| `git_config_mode`         | `"0644"`                    | File mode of `.gitconfig`.                                                  |
| `git_user_name`           | `""`                        | `user.name`. Omitted when empty.                                            |
| `git_user_email`          | `""`                        | `user.email`. Omitted when empty.                                           |
| `git_core_editor`         | `"vim"`                     | `core.editor`.                                                              |
| `git_core_autocrlf`       | `"input"`                   | `core.autocrlf`: `true`, `false` or `input`.                                |
| `git_help_autocorrect`    | `1`                         | `help.autocorrect`: deciseconds, or `never`, `immediate`, `prompt`.         |
| `git_color_ui`            | `"auto"`                    | `color.ui`.                                                                 |
| `git_push_default`        | `"simple"`                  | `push.default`.                                                             |
| `git_init_default_branch` | `"main"`                    | `init.defaultBranch`.                                                       |
| `git_pull_rebase`         | `"false"`                   | `pull.rebase`: `true`, `false`, `merges` or `interactive`.                  |
| `git_credential_helper`   | `"!gh auth git-credential"` | `credential.helper` for `https://github.com` and `https://gist.github.com`. |

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
      git_core_editor: "code --wait"
      git_pull_rebase: "true"
```

## Role Idempotency

True. A second run with the same variables reports no changes. Check mode is supported.

## Role Atomicity

True. `.gitconfig` is written atomically by `ansible.builtin.template`.

## Roll-back capabilities

None. The previous `.gitconfig` is not backed up, and the git package is not removed.

## License

GPL-2.0-or-later

## Author Information

Krishana Pal Singh Chouhan
