# Ansible Role: User Setup

[![CI](https://github.com/pluggero/ansible-role-user-setup/actions/workflows/ci.yml/badge.svg)](https://github.com/pluggero/ansible-role-user-setup/actions/workflows/ci.yml) [![Ansible Galaxy downloads](https://img.shields.io/ansible/role/d/pluggero/user_setup?label=Galaxy%20downloads&logo=ansible&color=%23096598)](https://galaxy.ansible.com/ui/standalone/roles/pluggero/user_setup)

An Ansible Role that performs a basic user setup on different Linux distributions and FreeBSD.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
user_setup_groups:
  - name: test
    system: false
    state: present
```

The groups to create can be defined in the variable `user_setup_groups`. Set `state: absent` to remove a group.

```yaml
user_setup_users:
  - name: "{{ ansible_user }}"
    state: present
    uid: 1500
    shell: "/bin/bash"
    create_home: true
    groups: []
    groups_append: true
    password: "{{ user_setup_example_password | password_hash('sha512', user_setup_example_password_salt) }}"
    sudo_access: true
    sudo_nopasswd: false
    env_var_setup: true
    env_var_file: ".env_vars"
    user_directories:
      - name: ".local/share/archive"
        env_var: "XDG_ARCHIVE_DIR"
      - name: ".config"
        env_var: "XDG_CONFIG_DIR"
      - name: "desktop"
        env_var: "XDG_DESKTOP_DIR"
      - name: "documents"
        env_var: "XDG_DOCUMENTS_DIR"
      - name: "downloads"
        env_var: "XDG_DOWNLOAD_DIR"
      - name: "music"
        env_var: "XDG_MUSIC_DIR"
      - name: "pictures"
        env_var: "XDG_PICTURES_DIR"
      - name: "tools"
        env_var: "XDG_TOOLS_DIR"
      - name: "videos"
        env_var: "XDG_VIDEOS_DIR"
    ssh_directory: ".ssh"
    ssh_authorized_keys_exclusive: false
    ssh_authorized_keys:
      - key: "ssh-ed25519 AAAA... user@laptop"
        options: 'from="192.168.1.0/24",no-agent-forwarding'
      - key: "ssh-rsa AAAA... user@backup"
```

The users to create can be defined in the variable `user_setup_users`.
In the example above the `user_setup_example_password` and `user_setup_example_password_salt` should be stored securely in a vault.

**User State Management:**

- `state` (default: `present`): Controls user lifecycle:
  - When `present`: Creates or updates the user with the specified configuration.
  - When `absent`: Removes the user and their home directory. Also removes associated sudoers files.

To remove a user:

```yaml
user_setup_users:
  - name: olduser
    state: absent
```

**Home Directory Management:**

- `create_home` (default: `true`): Controls whether a home directory is created for the user:
  - When `true`: Creates a home directory for the user at `/home/<username>` (default behavior).
  - When `false`: Does not create a home directory. The home directory path in `/etc/passwd` is set to `/nonexistent`, which is useful for service accounts and system users.

To create a user without a home directory (e.g., for a service account):

```yaml
user_setup_users:
  - name: serviceuser
    create_home: false
```

**SSH Authorized Keys Management:**

- `ssh_authorized_keys_exclusive` (default: `false`): Controls how SSH keys are managed:
  - When `true`: The role creates a complete authorized_keys file containing only the keys specified in `ssh_authorized_keys`. Any existing keys not in this list will be removed.
  - When `false`: Keys are added to the authorized_keys file without removing any existing keys (append-only mode).
- SSH keys can be specified as:
  - Direct key strings: `"ssh-ed25519 AAAA... user@host"`
- Key options can be specified using the `options` parameter for fine-grained access control (e.g., `from="192.168.1.0/24",no-agent-forwarding`).

**FreeBSD-Specific Behavior:**

- Users with `sudo_access: true` are automatically added to the `wheel` group (required for privilege escalation on FreeBSD).
- The role uses `/usr/local/sbin/visudo` for sudoers file validation on FreeBSD (instead of `/usr/sbin/visudo`).

## Dependencies

None.

## Example Playbook

```yaml
- hosts: all
  roles:
    - pluggero.user_setup
```

## License

MIT / BSD

## Author Information

This role was created in 2025 by Robin Plugge.
