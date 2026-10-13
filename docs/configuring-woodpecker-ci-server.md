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
SPDX-FileCopyrightText: 2023 Alejandro AR
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

See the project's [documentation](https://woodpecker-ci.org/docs/intro) to learn what Woodpecker CI agent does and why it might be useful to you.

## Prerequisites

To run a Woodpecker CI server instance it is necessary to prepare a database. You can use a [MySQL](https://www.mysql.com/) compatible database server, [Postgres](https://www.postgresql.org/), or [SQLite](https://www.sqlite.org/). The SQLite database file will be automatically created by the service if it is enabled.

If you are looking for Ansible roles for a MySQL compatible server or Postgres, you can check out [ansible-role-mariadb](https://github.com/mother-of-all-self-hosting/ansible-role-mariadb) and [ansible-role-postgres](https://github.com/mother-of-all-self-hosting/ansible-role-postgres), both of which are maintained by the [Mother-of-All-Self-Hosting (MASH)](https://github.com/mother-of-all-self-hosting) team.

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

### Set the hostname

To enable the Woodpecker CI server you need to set the hostname as well. To do so, add the following configuration to your `vars.yml` file. Make sure to replace `example.com` with your own value.

```yaml
woodpecker_ci_server_hostname: "example.com"
```

After adjusting the hostname, make sure to adjust your DNS records to point the domain to your server.

### Set a token for server

You also need to specify a shared secret used by the server to authenticate communication by adding the following configuration to your `vars.yml` file. The value can be generated with `openssl rand -hex 32` or in another way.

```yaml
woodpecker_ci_server_config_agent_secret: YOUR_SHARED_SECRET_HERE
```

>[!NOTE]
> If you choose to share this secret with an agent, the server will register it as a non-persistent agent.

Refer to [this page](https://woodpecker-ci.org/docs/administration/configuration/server) on the official documentation for details about how to register an server and obtain a secret for it.

### Set username matching VCS

It is necessary to specify one or more usernames that match your version control system (e.g. Gitea) as below:

```yaml
woodpecker_ci_server_config_admins:
  - YOUR_USERNAME_HERE
  - ANOTHER_USERNAME_HERE
```

>[!NOTE]
> These users will have admin privileges upon signup.

### Set a source-code provider name

You also have to specify a source-code provider (GitHub, Gitlab, Gitea, Forgejo, etc.) by adding the following configuration to your `vars.yml` file.

```yaml
woodpecker_ci_server_provider: SOURCE_CODE_PROVIDER_NAME_HERE
```

### Connecting to agents on remote machines (optional)

If Woodpecker CI agents will run on remote machines, add the following configuration to your `vars.yml` file:

```yaml
woodpecker_ci_server_container_labels_traefik_grpc_enabled: true
````

### Configuring database

#### Specify database (optional)

You can specify a database used by Forgejo. By default it is configured to use SQLite.

To use Postgres, add the following configuration to your `vars.yml` file:

```yaml
woodpecker_ci_server_database_type: postgres
```

Set `mysql` to use a MySQL compatible database.

For other settings, check variables such as `woodpecker_ci_server_database_mysql_*` and `woodpecker_ci_server_database_postgres_*` on [`defaults/main.yml`](../defaults/main.yml).

#### Configuring connection to the database server (optional)

By default the role is configured to establish the connection to the database server via a Unix socket. You can mount the Unix socket by adding the following configuration to your `vars.yml` file:

```yaml
# Specify the path to the MySQL compatible server's Unix socket path on the host (bind-mount source)
woodpecker_ci_server_database_mysql_socket_path_host: ""

# Specify the path to the Postgres Unix socket path on the host (bind-mount source)
woodpecker_ci_server_database_postgres_socket_path_host: ""
```

Setting it enables to connect to the database server via Unix socket mounted in the container.

If TCP connection is preferred, connection via the Unix socket can be disabled by adding the following configuration to your `vars.yml` file:

```yaml
# Disable the connection to the MySQL compatible server via a Unix socket
woodpecker_ci_server_database_mysql_socket_enabled: false

# Disable the connection to the Postgres server via a Unix socket
woodpecker_ci_server_database_postgres_socket_enabled: false
```

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
