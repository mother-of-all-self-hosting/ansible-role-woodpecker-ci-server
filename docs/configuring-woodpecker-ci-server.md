<!--
SPDX-FileCopyrightText: 2020 Aaron Raimist
SPDX-FileCopyrightText: 2020 Chris van Dijk
SPDX-FileCopyrightText: 2020 Dominik Zajac
SPDX-FileCopyrightText: 2020 Mickaël Cornière
SPDX-FileCopyrightText: 2020-2024 MDAD project contributors
SPDX-FileCopyrightText: 2020-2025 Slavi Pantaleev
SPDX-FileCopyrightText: 2022 François Darveau
SPDX-FileCopyrightText: 2022 Julian Foad
SPDX-FileCopyrightText: 2022 Warren Bailey
SPDX-FileCopyrightText: 2023 Antonis Christofides
SPDX-FileCopyrightText: 2023 Felix Stupp
SPDX-FileCopyrightText: 2023 Julian-Samuel Gebühr
SPDX-FileCopyrightText: 2023 MASH project contributors
SPDX-FileCopyrightText: 2023 Pierre 'McFly' Marty
SPDX-FileCopyrightText: 2024 Sergio Durigan Junior
SPDX-FileCopyrightText: 2024 Thomas Miceli
SPDX-FileCopyrightText: 2024-2026 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setting up Woodpecker CI agent

This is an [Ansible](https://www.ansible.com/) role which installs the **agent** component of [Woodpecker CI](https://woodpecker-ci.org/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

Woodpecker CI is a [Continuous Integration](https://en.wikipedia.org/wiki/Continuous_integration) engine which can build and deploy your code automatically after pushing to a [Gitea](gitea.md) or [Forgejo](forgejo.md) repository.

See the project's [documentation](https://www.woodpecker-ci-agent.com/docs) to learn what Woodpecker CI agent does and why it might be useful to you.

## Prerequisites

To run a Woodpecker CI agent instance it is necessary to prepare a Woodpecker CI server.

If you are looking for an Ansible role for a Woodpecker CI server, you can check out [ansible-role-woodpecker-ci-server](https://github.com/mother-of-all-self-hosting/ansible-role-woodpecker-ci-server) maintained by the [Mother-of-All-Self-Hosting (MASH)](https://github.com/mother-of-all-self-hosting) team.

## Adjusting the playbook configuration

To enable Woodpecker CI agent with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/mash.example.com/vars.yml` if you use the [MASH Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

```yaml
########################################################################
#                                                                      #
# woodpecker_ci_agent                                                  #
#                                                                      #
########################################################################

woodpecker_ci_agent_enabled: true

########################################################################
#                                                                      #
# /woodpecker_ci_agent                                                 #
#                                                                      #
########################################################################
```

### Set the hostname and port of the Woodpecker CI server

You also need to specify the Woodpecker CI server's gRPC hostname and port by adding the following configuration to your `vars.yml` file:

```yaml
woodpecker_ci_agent_config_server: YOUR_SERVER_HOSTNAME:YOUR_SERVER_PORT
```

### Connecting over secure gRPC channel (optional; recommended)

To have the agent connect to the server over a secure gRPC channel (recommended), add the following configuration to your `vars.yml` file:

```yaml
woodpecker_ci_agent_config_grpc_secure: true
````

You can have the agent to verify th server's TLS certificate when connecting over a secure gRPC channel by adding the following configuration to your `vars.yml` file:

```yaml
woodpecker_ci_agent_config_grpc_verify: true
```

### Setting a token for agent (optional)

You can specify a shared secret used by the server and agents to authenticate communication by adding the following configuration to your `vars.yml` file:

```yaml
woodpecker_ci_agent_config_agent_secret: YOUR_SHARED_SECRET_HERE
```

>[!NOTE]
> It is not recommended to use a shared secret between Woodpecker CI server and all of its agents.

Refer to [this page](https://woodpecker-ci.org/docs/administration/configuration/agent) on the official documentation for details about how to register an agent and obtain a secret for it.

### Extending the configuration

There are some additional things you may wish to configure about the service.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `woodpecker_ci_agent_environment_variables_additional_variables` variable

Refer to [the official documentation](https://woodpecker-ci.org/docs/usage/environment) for a complete list of Woodpecker CI agent's config options that you can put in `woodpecker_ci_agent_environment_variables_additional_variables`.

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, Woodpecker CI agent becomes available. The agent automatically registers with the Woodpecker CI server and takes jobs from it.

Refer to [this page](https://woodpecker-ci.org/docs/administration/configuration/agent) on the documentation about its usage.

## Troubleshooting

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu woodpecker-ci-agent` (or how you/your playbook named the service, e.g. `mash-woodpecker-ci-agent`).
