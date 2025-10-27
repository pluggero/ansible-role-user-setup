# Ansible Role: User Setup

[![CI](https://github.com/pluggero/ansible-role-user-setup/actions/workflows/ci.yml/badge.svg)](https://github.com/pluggero/ansible-role-user-setup/actions/workflows/ci.yml) [![Ansible Galaxy downloads](https://img.shields.io/ansible/role/d/pluggero/user_setup?label=Galaxy%20downloads&logo=ansible&color=%23096598)](https://galaxy.ansible.com/ui/standalone/roles/pluggero/user_setup)

An Ansible Role that performs a basic user setup on differnet Linux distributions.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
user_setup_groups:
  - name: test
    system: false
```

The groups to create can be defined in the variable `user_setup_groups`.

```yaml
user_setup_users:
  - name: "{{ ansible_user }}"
    uid: 1500
    shell: "/bin/bash"
    groups: []
    groups_append: true
    password: "{{ user_setup_example_password | password_hash('sha512', user_setup_example_password_salt) }}"
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
    ssh_authorized_keys:
      - key: "ssh-ed25519 AAAA... user@laptop"
        options: 'from="192.168.1.0/24",no-agent-forwarding'
      - key: "ssh-rsa AAAA... user@backup"
```

The users to create can be defined in the variable `user_setup_users`.
In the example above the `user_setup_example_password` and `user_setup_example_password_salt` should be stored securely in a vault.

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
