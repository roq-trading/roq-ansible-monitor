# roq-monitor

Ansible script to install a monitoring and alerting solution, including dashboard.

## Design

### Prometheus

Prometheus can scrape metrics from the Roq gateways and collect these into a
time-series database.

Prometheus is the database solution behind Grafana.

### Grafana

Grafana is a dashboard solution allowing you to build your own monitoring solution.


## Dedendencies

* [Ansible](https://www.ansible.com/)
* [Prometheus](https://prometheus.io/)
* [Grafana](https://grafana.com/)


## Prerequisites

### Ansible

If using conda, you can install ansible like this

```bash
conda install -y ansible
```

### Remote Host

This is the server you will install to.
It is identified by an IP address ("a.b.c.d") and you can log on with a user
("ansible") having sudo access.

### Inventory File

Ansible requires an inventory file (name is not important, but let's name it "example.yml")

```yaml
---
ungrouped:
  hosts:
    server:
      ansible_host: a.b.c.d
      ansible_user: ansible
      ansible_become_user: root
```

> We're using the label "server".

### Host Variables

If created, host specific variable will be imported from `host_vars/server.yml`.

> The filename is automatically matched to the label "server" from the inventory file.

This is the place to configure the services.

### Group Variables

Common variables can be found in `group_vars/all.yml`.

This file contains all the defaults.

> You can override the defaults by configuring host variables.


## Running

```bash
ansible-playbook -i example site.yml --ask-become-pass
```

## License

The project is released under the terms of the MIT license.
