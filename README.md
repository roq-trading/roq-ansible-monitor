# roq-monitor

!!! EXPERIMENTAL !!!


```bash
conda install ansible
```

Inventory file, e.g. `server`

```
[example]
server ansible_host="a.b.c.d" ansible_user="ansible" become_user="root"
```

> You should change "a.b.c.d" to the IP address of your server,
> and the "ansible" user can be changed to any user that has sudo access.

The configuration for the server can be found in `host_vars/server.yml`

> The filename is automatically matched by Ansible using the label `server` from
> the inventory.

For the example we include here a list of gateways that Prometheus should scrape.

You can add more configuration overriding the defaults you find in `group_vars/all.yml`.

Installing

```bash
ansible-playbook -i server site.yml --ask-become-pass
```

Gateway flags

```bash
--service_listen_address /run/roq/service/cme.sock
```

You can by default access nginx from localhost.
You will have to open a port, or create a tunnel, if you want to access nginx from another host.

End-points

```
http://localhost/prometheus/
```

```
http://localhost/grafana/
```

```
http://localhost/roq/service/cme/metrics
```


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
