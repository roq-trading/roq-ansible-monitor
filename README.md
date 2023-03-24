# roq-monitor

Ansible script to install a monitoring and alerting solution, including dashboard.

## Design

The easiest option is to install and run the NGINX-Prometheus-Grafana stack as containers.

### Podman

Podman is used, but it's just one of many solutions one could choose to manage containers.

> Podman is convenient because it allows users to manage containers without 
> becoming "root". Also, in our experience, it is less intrusive to the system than
> e.g. Docker. Finally, Podman's license terms are more open than Docker's.

> Just fork this project if Docker is your prefered solution. Podman is nearly 100%
> compatible with Docker. It is a relatively small undertaking to modify this script.

### NGINX

NGINX is useful as a front-end for the services without requiring ports to be
published to the host.

NGINX also solves the problem of bridging between Roq gateways running on the
host and Prometheus.

> We don't want the containers to run unconfined (have access to the host) and the
> only way to bridge metrics from the gateways (running natively on the host) into
> containers is to use unix sockets. Prometheus can't scrape from unix sockets.
> Our NGINX configuration provides that bridge.

### Prometheus

Prometheus can scrape metrics from the Roq gateways and collect these into a
time-series database.

Prometheus is the database solution behind Grafana.

### Grafana

Grafana is a dashboard solution allowing you to build your own monitoring solution.


## Depdendencies

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

Ansible requires an inventory file (not important, but let's name it "server")

```
[example]
server ansible_host="a.b.c.d" ansible_user="ansible" become_user="root"
```

> Note! We're using the label "server".

### Host Variables

If created, host specific variable will be imported from `host_vars/server.yml`.

> Note! The filename is automatically matched to the label "server" from the inventory file.

This is the place to configure the services.

### Group Variables

Common variables can be found in `group_vars/all.yml`.

This file contains all the defaults.

> Note! You can override the defaults by configuring host variables.


## Running

```bash
ansible-playbook -i server site.yml --ask-become-pass
```

## Using

### NGINX

NGINX can be reached from the remote host's localhost.

Access from other hosts may require

* Opening a port, if you use a firewall
* Setting up a tunnel (SSH or VPN)

You can now access either of the following end-points

* `http://localhost/grafana/`
* `http://localhost/prometheus/`
* `http://localhost/roq/service/<name>/metrics`

> Note! Remember to replace "localhost" or "<name>" as appropriate.

### Gateways

You should add this to your gateway flags

```bash
--service_listen_address /run/roq/service/<name>.sock
```

> Note! Remember to replace "<name>".

You can by default access nginx from localhost.
You will have to open a port, or create a tunnel, if you want to access nginx from another host.


## Nginx

### Reason #1

Prometheus is easily installed as a container.

However,

* We don't want Prometheus to run unconfined (having access to the host system)
* We do want the Roq gateways to run natively on the host

The easiest solution to give Prometheus access to gateway metrics is to mount
a directory into the container which includes unix sockets addresses exposed by
the gateways.

Unfortunately, Prometheus can not be configured to scrap from unix sockets, only
regular http IP end-points.

We therefore use Nginx to bridge from unix socket to regular IP.

### Reason #2

We could publish ports from Prometheus and Grafana to the host system.

However,

* Prometheus must be able to access Nginx
* Grafana must be able to access Prometheus
* We don't want either to run uncofined

We therefore create a container network so these services can communicat between
themselves and we only publish a port from Nginx to the host system.


## Prometheus

This is just one solution for capture time-series of gateway metrics.

Roq's gateways has support Prometheus.


## Grafana

A dashboard solution which can easily communicate with Prometheus.
