# roq-monitor

Ansible script to install a monitoring and alerting solution, including dashboard.

## Design

The easiest option is to install and run the NGINX-Prometheus-Grafana stack as containers.

### Podman

Podman is used, but it's just one of many solutions one could choose to manage containers.

> Podman is convenient because it allows users to manage containers without 
> becoming "root". Also, in our experience, it is less intrusive to the system than
> e.g. Docker. Finally, Podman's license terms are more open than Docker's.

> Just fork this project if Docker is your prefered solution. It is a relatively
> small undertaking to modify this script. Podman is nearly 100% compatible with Docker. 

### NGINX

NGINX is useful as a front-end for the services without requiring ports to be
published to the host.

NGINX also solves the problem of bridging between Prometheus (container) and the
Roq gateways (running on the host).

> We don't want the containers to run unconfined (have access to the host) and the
> only way to bridge metrics from the gateways (running natively on the host) into
> containers is by using unix sockets. However, Prometheus unfortunately can't
> scrape from unix sockets. Our NGINX configuration provides that bridge.

### Prometheus

Prometheus can scrape metrics from the Roq gateways and collect these into a
time-series database.

Prometheus is the database solution behind Grafana.

### Grafana

Grafana is a dashboard solution allowing you to build your own monitoring solution.


## Dedendencies

* [Ansible](https://www.ansible.com/)
* [Podman](https://podman.io/)
* [NGINX](https://www.nginx.com/)
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

Ansible requires an inventory file (name is not important, but let's name it "example")

```
[example]
server ansible_host="a.b.c.d" ansible_user="ansible" become_user="root"
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

## Using

### NGINX

NGINX can be reached from the remote host's "localhost" address.

> You have to be **ON** the remote host to use the "localhost" address.

Access from other hosts may require

* Opening a port, if you use a firewall
* Setting up a tunnel (SSH or VPN)

You can now access either of the following end-points

* `http://localhost/grafana/`
* `http://localhost/prometheus/`
* `http://localhost/roq/service/<name>/metrics`

> Remember to replace "localhost" and/or "name" as appropriate.

### Gateways

You should add this to your gateway flags

```bash
--service_listen_address /run/roq/service/<name>.sock
```

> Remember to replace "name" as appropriate.


## License

The project is released under the terms of the MIT license.
