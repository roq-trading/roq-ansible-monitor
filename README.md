# roq-monitor

!!! EXPERIMENTAL !!!


using ansible


## All

a (local) podman network is created


## Trading Host

gateways expose metrics to:
/run/roq/service/<name>.sock

nginx will bridge these to regular IP


## Monitoring Host

prometheus will access nginx (podman network or by IP)

grafana

nginx will make prometheus and grafana available


## Other

trading and monitoring hosts may be the same host
