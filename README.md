<!--
SPDX-FileCopyrightText: 2025 Slavi Pantaleev
SPDX-FileCopyrightText: 2026 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Headscale Ansible role

This is an [Ansible](https://www.ansible.com/) role which installs [Headscale](https://headscale.net) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

This role *implicitly* depends on:

- [`com.devture.ansible.role.playbook_help`](https://github.com/devture/com.devture.ansible.role.playbook_help)
- [`com.devture.ansible.role.systemd_docker_base`](https://github.com/devture/com.devture.ansible.role.systemd_docker_base)

Check [`defaults/main.yml`](defaults/main.yml) for the full list of supported options.

💡 For an Ansible playbook which integrates this role and makes it easier to use, see the [Mother-of-All-Self-Hosting Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

## Embedded DERP server

Headscale serves the embedded DERP relay through its normal HTTPS endpoint, but its STUN listener uses a separate UDP port. To publish the default UDP port from the container, use:

```yaml
headscale_config_derp_server_enabled: true
headscale_container_derp_stun_bind_port: 3478
```

The host firewall and any upstream NAT must also allow and forward UDP 3478. Headscale advertises the port from `headscale_config_derp_server_stun_listen_addr`, so that listen port must match `headscale_container_derp_stun_port` and be reachable externally under the same public port number. For direct Docker publication, the host port in `headscale_container_derp_stun_bind_port` should also match. A different Docker host port only works when upstream NAT maps the advertised public port to it. Headscale does not currently support configuring a separate advertised STUN port.

By default, `headscale_config_derp_server_ipv4` and `headscale_config_derp_server_ipv6` are empty, so clients resolve `headscale_hostname`. Set either variable to the embedded DERP server's actual public address when explicit IP advertisement is desired.

## Development

### pre-commit

You can optionally install a Git pre-commit hook (via [mise](https://mise.jdx.dev/) + [prek](https://prek.j178.dev/)) that runs formatting and linting checks before each commit. See [`.pre-commit-config.yaml`](./.pre-commit-config.yaml) for which hooks are to be executed.

To install the hook, run the [`just`](https://github.com/casey/just) command below:

```sh
just prek-install-git-pre-commit-hook
```

### Molecule

This role supports [Molecule](https://docs.ansible.com/projects/molecule/), an Ansible testing framework designed for developing and testing Ansible collections, playbooks, and roles.

Refer to [this page](./molecule/README.md) for details about how to utilize it.
