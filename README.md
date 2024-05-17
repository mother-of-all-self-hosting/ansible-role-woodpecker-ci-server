# Woodpecker CI Server Ansible role

This is an [Ansible](https://www.ansible.com/) role which installs the **server** component of [Woodpecker CI](https://woodpecker-ci.org/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

This role *implicitly* depends on [`com.devture.ansible.role.systemd_docker_base`](https://github.com/devture/com.devture.ansible.role.systemd_docker_base).

The **agent** component of Woodpecker CI is managed by a sibling role - [com.devture.ansible.role.woodpecker_ci_agent](https://github.com/devture/com.devture.ansible.role.woodpecker_ci_agent).

For an Ansible playbook which integrates this role and makes it easier to use, see the [mash-playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).
