<!--
SPDX-FileCopyrightText: 2022, 2023 Slavi Pantaleev
SPDX-FileCopyrightText: 2026 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Woodpecker CI server Ansible role

This is an [Ansible](https://www.ansible.com/) role which installs the **server** component of [Woodpecker CI](https://woodpecker-ci.org/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

This role *implicitly* depends on:

- [`com.devture.ansible.role.systemd_docker_base`](https://github.com/devture/com.devture.ansible.role.systemd_docker_base)

The **agent** component of Woodpecker CI is managed by a sibling role — [mother-of-all-self-hosting/ansible-role-woodpecker-ci-agent](https://github.com/mother-of-all-self-hosting/ansible-role-woodpecker-ci-agent).

💡 For an Ansible playbook which integrates this role and makes it easier to use, see the [Mother-of-All-Self-Hosting Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

## Development

You can optionally install [pre-commit](https://pre-commit.com/) so that simple mistakes are checked and noticed before changes are pushed to a remote branch. See [`.pre-commit-config.yaml`](./.pre-commit-config.yaml) for which hooks are to be executed.

See [this section](https://pre-commit.com/#usage) on the official documentation for usage.
