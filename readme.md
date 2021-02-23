### Inspired by


> https://blog.networktocode.com/post/monitoring_websites_with_telegraf_and_prometheus/

### Test Telegraf to run locally with all the telegraf syntax

```shell
### install telegraf
$ brew install telegraf
### Test run locally
$ telegraf --config telegraf.conf

### Test if everything works
$ curl localhost:9016/metrics

### run test once
$ telegraf --config telegraf.conf --test

```

### Design

1. Must run on Kubernetes easily, single yaml file
2. Must able to monitor multiple sites, the config shall be flexible (configmap)
3. All secret shall hide in side a Kubernetes secret, and the values shall mount as ENV.
4. Telegraf config is able to read ENV as part of the config file
5. "secret" can be web authentication credential but in this simplified case, mount it as a monitoring target

### Install on Kubernetes


```shell
$ kubectl apply -f telegarf.yml

```

### Delete from Kubernetes


```shell
$ kubectl apply -f telegarf.yml

```

### Prometheus target

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'telegraf website'
    scrape_interval: 10s
    static_configs:
      - targets:
        - "[telegraf_host]:9016"
```

### Grafana notes

What does it look like to get this information into a graph on Grafana?

Grafana - Websites

Grafana Website Response

To build this chart, this is a small configuration. In the Metrics section I only put the query of http_response_response_time. With the legend I set it to {{ server }} to get the website address as the table legend.

In the visualization section, the only thing that is needs to be done is to adjust in the Left Y Axis Unit to be seconds (s) to provide the proper Y-Axis Metric.

Grafana - DNS

Grafana DNS Response

This is going to be another small configuration panel, similar to the previous one. In the Metrics section the corresponding query to get response time is dns_query_query_time_ms. The legend you then set to {{ domain }} to match that of what is in the query shown above.

In the visualization section, you should use the Unit of milliseconds (ms). If you copied the panel from the Website panel, don’t forget to change this. The unit of measure is in fact different and the time scale would be off.
