# Ansible Role: Restic

Ansible role for installing and configuring [Restic](https://restic.net/) backups.

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


| Variable                    | Description                                                                 | Default Value |
|-----------------------------|-----------------------------------------------------------------------------|---------------|
| `restic_version`            | Use a specific version of Restic, eg. `0.11.0`. Specify `false` for latest. | `false`       |
| `restic_install_dir`        | Installation directory for Terraform.                                       | `$HOME/bin`   |
| `restic_install_hard_links` | Install using hard links rather than symbolic links.                        | `false`       |

## Dependencies

No dependencies on other roles.

## Example Playbook

Example playbook for installing to single user:

```yaml
---

- hosts: all
  roles:
    - role: xanmanning.restic
      vars:
        restic_version: 0.11.0
```

Example playbook for installing the latest Restic version globally:

```yaml
---

- hosts: all
  become: true
  roles:
    - role: xanmanning.restic
      vars:
        restic_install_os_dependencies: true
        restic_install_dir: /opt/restic/bin
```

## License

[BSD 3-clause](LICENSE.txt)

## Author Information

[Xan Manning](https://xan.manning.io/)
