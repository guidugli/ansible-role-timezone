[![CI](https://github.com/guidugli/ansible-role-timezone/actions/workflows/CI.yml/badge.svg)](https://github.com/guidugli/ansible-role-timezone/actions/workflows/CI.yml)
[![Release](https://img.shields.io/github/v/tag/guidugli/ansible-role-timezone)](https://github.com/guidugli/ansible-role-timezone/tags)
[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-guidugli.timezone-blue)](https://galaxy.ansible.com/ui/standalone/roles/guidugli/timezone/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

# Ansible Role: timezone

Install timezone data and configure the system IANA timezone on supported Linux hosts. The role is intentionally small,
idempotent, and execution-context aware: it never enforces privilege escalation itself and relies on the caller to run with
the required privileges when system-level changes are needed.

## Requirements

- Ansible Core 2.14 or newer, as declared by the role metadata.
- The `community.general` collection version `>=9.0.0` for `community.general.timezone`.
- The `containers.podman` collection version `>=1.10.0` for Molecule container scenarios.
- Target hosts must support package management for `tzdata` and operating system timezone configuration files or services.

## Variables

### `timezone_name`

- Type: `str`
- Default: `America/Sao_Paulo`
- Required: no

IANA timezone name applied by the role. Use values such as `UTC`, `Etc/UTC`, `America/Sao_Paulo`, or another valid entry
from the IANA time zone database. The role validates that the value is a non-empty string before applying it.

### `timezone_hwclock`

- Type: `str`
- Default: undefined
- Required: no
- Choices: `UTC`, `local`

Optional hardware clock mode passed to `community.general.timezone`. Leave this variable undefined to preserve the current
hardware clock configuration. This setting can be unsupported or undesirable in some virtualized and cloud environments.

## Example Playbook

```yaml
---
- name: Configure host timezone
  hosts: all
  become: true
  roles:
    - role: guidugli.timezone
      vars:
        timezone_name: UTC
```

To preserve the current hardware clock mode, omit `timezone_hwclock`. To set it explicitly, pass one of the supported
values:

```yaml
---
- name: Configure host timezone and hardware clock mode
  hosts: all
  become: true
  roles:
    - role: guidugli.timezone
      vars:
        timezone_name: America/Sao_Paulo
        timezone_hwclock: UTC
```

## Molecule Testing

The role uses shared Molecule playbooks under `molecule/shared` and scenario-specific container setup under
`molecule/default` and `molecule/systemd`.

Run the standard scenario:

```bash
molecule test -s default
```

Run the systemd-oriented scenario:

```bash
molecule test -s systemd
```

The shared verify play checks that the requested zoneinfo file exists and that the configured timezone is reflected through
`/etc/localtime` or Debian-style `/etc/timezone` evidence.

## Execution Notes

### Privilege model

This role does not set `become`, `become_user`, or `become_method` in role tasks. Installing `tzdata` and changing system
timezone state are privileged operations on normal hosts, so callers should enable privilege escalation externally, as shown
in the example playbooks.

### Container and systemd behavior

Molecule containers execute as root and therefore do not require role-level privilege escalation. Timezone configuration may
interact with system facilities such as `/etc/localtime`, `/etc/timezone`, or systemd-backed timezone tooling depending on
the target distribution and container capabilities. The systemd Molecule scenario exists to exercise roles in a
systemd-capable container context without embedding systemd assumptions in the role tasks.
