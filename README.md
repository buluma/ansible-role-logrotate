# [Ansible role logrotate](#logrotate)

Install and configure logrotate on your system.

|GitHub|GitLab|Downloads|Version|Issues|Pull Requests|
|------|------|-------|-------|------|-------------|
|[![github](https://github.com/buluma/ansible-role-logrotate/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-logrotate/actions/workflows/molecule.yml)|[![gitlab](https://gitlab.com/shadowwalker/ansible-role-logrotate/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-logrotate)|[![downloads](https://img.shields.io/ansible/role/d/4754)](https://galaxy.ansible.com/buluma/logrotate)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-logrotate.svg)](https://github.com/buluma/ansible-role-logrotate/releases/)|[![Issues](https://img.shields.io/github/issues/buluma/ansible-role-logrotate.svg)](https://github.com/buluma/ansible-role-logrotate/issues/)|[![PullRequests](https://img.shields.io/github/issues-pr-closed-raw/buluma/ansible-role-logrotate.svg)](https://github.com/buluma/ansible-role-logrotate/pulls/)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/buluma/ansible-role-logrotate/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: converge
  hosts: all
  become: yes
  gather_facts: yes

  vars:
    logrotate_frequency: daily
    logrotate_keep: 7
    logrotate_compress: yes
    logrotate_entries:
      - name: example
        path: "/var/log/example/*.log"
      - name: example-frequency
        path: "/var/log/example-frequency/*.log"
        frequency: weekly
      - name: example-keep
        path: "/var/log/example-keep/*.log"
        keep: 14
      - name: example-compress
        path: "/var/log/example-compress/*.log"
        compress: yes
      - name: example-script
        path: "/var/log/example-script/*.log"
        postrotate: killall -HUP some_process_name
      - name: btmp
        path: /var/log/btmp
        missingok: yes
        frequency: monthly
        create: yes
        create_mode: "0660"
        create_user: root
        create_group: utmp
        keep: 1
      - name: wtmp
        path: /var/log/wtmp
        missingok: yes
        frequency: monthly
        create: yes
        create_mode: "0664"
        create_user: root
        create_group: utmp
        minsize: 1M
        keep: 1
      - name: dnf
        path: /var/log/hawkey.log
        missingok: yes
        notifempty: yes
        keep: 4
        frequency: weekly
        create: yes
      - name: example-sharedscripts
        path: "/var/log/example-sharedscripts/*.log"
        sharedscripts: yes

  roles:
    - role: buluma.logrotate
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/buluma/ansible-role-logrotate/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: prepare
  hosts: all
  become: yes
  gather_facts: no

  roles:
    - role: buluma.bootstrap
    - role: buluma.cron

  post_tasks:
    - name: Create log directory
      ansible.builtin.file:
        path: "{{ item }}"
        state: directory
      loop:
        - /var/log/example
        - /var/log/example-frequency
        - /var/log/example-keep
        - /var/log/example-compress
        - /var/log/example-script
        - /var/log/example-sharedscripts

    - name: Create log file
      ansible.builtin.copy:
        dest: "{{ item }}"
        content: "example"
      loop:
        - /var/log/example/app.log
        - /var/log/example-frequency/app.log
        - /var/log/example-keep/app.log
        - /var/log/example-compress/app.log
        - /var/log/example-script/app.log
        - /var/log/example-sharedscripts/app.log
        - /var/log/btmp
        - /var/log/wtmp
        - /var/log/hawkey.log
```

Also see a [full explanation and example](https://buluma.github.io/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/buluma/ansible-role-logrotate/blob/master/defaults/main.yml):

```yaml
---
# defaults file for logrotate

# How often to rotate logs, either daily, weekly or monthly.
logrotate_frequency: weekly

# How many files to keep.
logrotate_keep: 4

# Should rotated logs be compressed??
logrotate_compress: yes

# User/Group for rotated log files (Loaded by OS-Specific vars if found, or and can be set manually)
logrotate_user: "{{ _logrotate_user[ansible_distribution] | default(_logrotate_user['default'] ) }}"
logrotate_group: "{{ _logrotate_group[ansible_distribution] | default(_logrotate_group['default'] ) }}"
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/buluma/ansible-role-logrotate/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | GitLab |
|-------------|--------|--------|
|[buluma.bootstrap](https://galaxy.ansible.com/buluma/bootstrap)|[![Build Status GitHub](https://github.com/buluma/ansible-role-bootstrap/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/ansible-role-bootstrap/actions)|[![Build Status GitLab](https://gitlab.com/shadowwalker/ansible-role-bootstrap/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-bootstrap)|
|[buluma.cron](https://galaxy.ansible.com/buluma/cron)|[![Build Status GitHub](https://github.com/buluma/ansible-role-cron/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/ansible-role-cron/actions)|[![Build Status GitLab](https://gitlab.com/shadowwalker/ansible-role-cron/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-cron)|
|[buluma.epel](https://galaxy.ansible.com/buluma/epel)|[![Build Status GitHub](https://github.com/buluma/ansible-role-epel/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/ansible-role-epel/actions)|[![Build Status GitLab](https://gitlab.com/shadowwalker/ansible-role-epel/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-epel)|

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://buluma.github.io/) for further information.

Here is an overview of related roles:

![dependencies](https://raw.githubusercontent.com/buluma/ansible-role-logrotate/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/buluma):

|container|tags|
|---------|----|
|[Alpine](https://hub.docker.com/repository/docker/buluma/alpine/general)|all|
|[Amazon](https://hub.docker.com/repository/docker/buluma/amazonlinux/general)|Candidate|
|[EL](https://hub.docker.com/repository/docker/buluma/enterpriselinux/general)|all|
|[Debian](https://hub.docker.com/repository/docker/buluma/debian/general)|all|
|[Fedora](https://hub.docker.com/repository/docker/buluma/fedora/general)|all|
|[opensuse](https://hub.docker.com/repository/docker/buluma/opensuse/general)|all|
|[Ubuntu](https://hub.docker.com/repository/docker/buluma/ubuntu/general)|all|
|[Kali](https://hub.docker.com/repository/docker/buluma/kali/general)|all|

The minimum version of Ansible required is 2.12, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/buluma/ansible-role-logrotate/issues)

## [Changelog](#changelog)

[Role History](https://github.com/buluma/ansible-role-logrotate/blob/master/CHANGELOG.md)

## [License](#license)

[Apache-2.0](https://github.com/buluma/ansible-role-logrotate/blob/master/LICENSE).

## [Author Information](#author-information)

[Michael Buluma](https://buluma.github.io/)

Please consider [sponsoring me](https://github.com/sponsors/buluma).

### [Special Thanks](#special-thanks)

Template inspired by [Robert de Bock](https://github.com/robertdebock)
