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

# Setting up Woodpecker CI server

This is an [Ansible](https://www.ansible.com/) role which installs the **server** component of [Woodpecker CI](https://woodpecker-ci.org/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

Woodpecker CI is a [Continuous Integration](https://en.wikipedia.org/wiki/Continuous_integration) engine which can build and deploy your code automatically after pushing to a [Gitea](gitea.md) or [Forgejo](forgejo.md) repository.

See the project's [documentation](https://www.woodpecker-ci-server.com/docs) to learn what Woodpecker CI server does and why it might be useful to you.

## Prerequisites

To run a Woodpecker CI server instance it is necessary to prepare a Woodpecker CI server.

If you are looking for an Ansible role for a Woodpecker CI server, you can check out [ansible-role-woodpecker-ci-server](https://github.com/mother-of-all-self-hosting/ansible-role-woodpecker-ci-server) maintained by the [Mother-of-All-Self-Hosting (MASH)](https://github.com/mother-of-all-self-hosting) team.

## Adjusting the playbook configuration

To enable Woodpecker CI server with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/mash.example.com/vars.yml` if you use the [MASH Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

```yaml
########################################################################
#                                                                      #
# woodpecker_ci_server                                                 #
#                                                                      #
########################################################################

woodpecker_ci_server_enabled: true

########################################################################
#                                                                      #
# /woodpecker_ci_server                                                #
#                                                                      #
########################################################################
```

### Connecting over secure gRPC channel (optional; recommended)

To have the server connect to the server over a secure gRPC channel (recommended), add the following configuration to your `vars.yml` file:

```yaml
woodpecker_ci_server_config_grpc_secure: true
````

### Setting a token for server (optional)

You can specify a shared secret used by the server and servers to authenticate communication by adding the following configuration to your `vars.yml` file:

```yaml
woodpecker_ci_server_config_server_secret: YOUR_SHARED_SECRET_HERE
```

>[!NOTE]
> It is not recommended to use a shared secret between Woodpecker CI server and all of its servers.

Refer to [this page](https://woodpecker-ci.org/docs/administration/configuration/server) on the official documentation for details about how to register an server and obtain a secret for it.

### Extending the configuration

There are some additional things you may wish to configure about the service.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `woodpecker_ci_server_environment_variables_additional_variables` variable

Refer to [the official documentation](https://woodpecker-ci.org/docs/usage/environment) for a complete list of Woodpecker CI server's config options that you can put in `woodpecker_ci_server_environment_variables_additional_variables`.

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, Woodpecker CI server becomes available. The server automatically registers with the Woodpecker CI server and takes jobs from it.

Refer to [this page](https://woodpecker-ci.org/docs/administration/configuration/server) on the documentation about its usage.

## Troubleshooting

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu woodpecker-ci-server` (or how you/your playbook named the service, e.g. `mash-woodpecker-ci-server`).
