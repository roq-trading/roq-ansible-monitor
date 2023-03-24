# roq-monitor

!!! EXPERIMENTAL !!!


```bash
conda install ansible
```

Inventory file, e.g. `server`

```
[SERVER]
server ansible_host="a.b.c.d" ansible_user="ansible" become_user="root"
```

> You should change "a.b.c.d" to the IP address of your server.

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
