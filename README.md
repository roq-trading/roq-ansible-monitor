# roq-monitor

!!! EXPERIMENTAL !!!


```bash
conda install ansible
```

Inventory file, e.g. `server`

```
[SERVER]
server ansible_host="192.168.188.50" ansible_user="ansible" become_user="root"

[monitoring]
server
```

Installing

```bash
ansible-playbook -i server site.yml --ask-become-pass
```
