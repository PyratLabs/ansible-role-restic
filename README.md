# Ansible Role: Restic

Ansible role for installing and configuring [Restic](https://restic.net/) backups.

[![CI](https://github.com/PyratLabs/ansible-role-restic/actions/workflows/ci.yml/badge.svg)](https://github.com/PyratLabs/ansible-role-restic/actions/workflows/ci.yml)

## Requirements

This role has been tested on Ansible 2.9.0+ against the following Linux Distributions:

  - Amazon Linux 2
  - CentOS 8
  - CentOS 7
  - Debian 10
  - Fedora 29
  - Fedora 30
  - Fedora 31
  - Ubuntu 18.04 LTS

The role will likely run against:

  - MacOS
  - FreeBSD
  - OpenBSD
  - Solaris

Windows, you will need to run this from WSL.

## Disclaimer

If you have any problems please create a GitHub issue, I maintain this role in
my spare time so I cannot promise a speedy fix delivery.

## Role Variables

| Variable                          | Description                                                                 | Default value   |
|-----------------------------------|-----------------------------------------------------------------------------|-----------------|
| `restic_version`                  | Use a specific version of Restic, eg. `0.11.0`. Specify `false` for latest. | `false`         |
| `restic_install_dir`              | Installation directory for Restic.                                          | `$HOME/bin`     |
| `restic_install_hard_links`       | Install using hard links rather than symbolic links.                        | `false`         |
| `restic_systemd_system_context`   | Install systemd timer units to system context.                              | `false`         |
| `restic_repository`               | Restic repository path or URI.                                              | NULL            |
| `restic_repository_password`      | Restic repository password.                                                 | NULL            |
| `restic_env_vars`                 | Dictionary of environment vars to pass to Restic. See examples.             | `{}`            |
| `restic_backup_schedule`          | Schedule for running backups, see `OnCalendar` for systemd.timer.           | `*-*-* 0:00:00` |
| `restic_systemd_randomised_delay` | Value for `RandomizedDelaySec`.                                             | 0               |
| `restic_backup_tag`               | Tag to apply to backup.                                                     | "automatic"     |
| `restic_files`                    | List of paths to backup.                                                    | []              |
| `restic_exclude_files`            | List of paths to exclude from backup.                                       | []              |
| `restic_backup_opts`              | List of options for your chosen backup repository.                          | []              |
| `restic_keep_last`                | Keep the last 'n' backups.                                                  | NULL            |
| `restic_keep_hourly`              | Keep the last 'n' hours of backups.                                         | NULL            |
| `restic_keep_daily`               | Keep the last 'n' days of backups.                                          | NULL            |
| `restic_keep_weekly`              | Keep the last 'n' weeks of backups.                                         | NULL            |
| `restic_keep_monthly`             | Keep the last 'n' months of backups.                                        | NULL            |
| `restic_keep_yearly`              | Keep the last 'n' years of backups.                                         | NULL            |
| `restic_keep_within_duration`     | Keep snapshots within this duration. eg. `2y5m7d3h`                         | NULL            |

## Dependencies

No dependencies on other roles.

## Example Playbook

### Simple playbook

The bare minimum to install and configure Restic, with no scheduled backups.

```yaml
---

- hosts: all
  become: true
  roles:
    - role: xanmanning.restic
      vars:
        restic_repository: "/backups"
        restic_repository_password: S0m3th?nGo0d_
```

### Home directory backup

Example playbook for installing a specific version to a user account, backing
up the user's home directory to remote SFTP on the first Thursday of the month
at 10:00am.

```yaml
---

- hosts: all
  roles:
    - role: xanmanning.restic
      vars:
        restic_version: 0.11.0
        restic_repository: "sftp:backup@backupserver:/backups/{{ ansible_user }}"
        restic_repository_password: S0m3th?nGo0d_
        restic_backup_schedule: "Thu *-*-1..7 10:00:00"
        restic_files:
          - "{{ ansible_user_dir }}"
```

### System backup to S3

Example playbook for installing the latest Restic version globally, uploading
to an AWS S3 bucket, using reduced redundancy and self pruning.

```yaml
---

- hosts: all
  become: true
  roles:
    - role: xanmanning.restic
      vars:
        restic_install_hard_links: true
        restic_install_dir: /usr/bin
        restic_repository: "s3:s3.amazonaws.com/my_bucket"
        restic_repository_password: S0m3th?nGo0d_
        restic_systemd_randomised_delay: 10min
        restic_keep_daily: 7
        restic_keep_weekly: 4
        restic_keep_monthly: 6
        restic_keep_yearly: 1
        restic_env_vars:
          AWS_ACCESS_KEY_ID: AKxxxxxxxxxxxxxxxxDF
          AWS_SECRET_ACCESS_KEY: fJz/xxxxxxxxxxxxxxxxxxx23
        restic_backup_opts:
          - s3.storage-class=REDUCED_REDUNDANCY
          - s3.region=eu-west-1
        restic_files:
          - /home
          - /root
          - /etc
          - /var/log
        restic_exclude_files:
          - /home/ansible
          - /root/bin

```

## License

[BSD 3-clause](LICENSE.txt)

## Author Information

[Xan Manning](https://xan.manning.io/)
