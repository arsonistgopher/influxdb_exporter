# InfluxDB Exporter [![Build Status](https://travis-ci.org/arsonistgopher/influxdb_exporter.svg)][travis]

[![CircleCI](https://circleci.com/gh/arsonistgopher/influxdb_exporter/tree/master.svg?style=shield)][circleci]

An exporter for metrics in the InfluxDB format used since 0.9.0. It collects
metrics in the
[line protocol][line_protocol] via a HTTP API,
transforms them and exposes them for consumption by Prometheus.

This exporter supports float, int and boolean fields. Tags are converted to Prometheus labels.

The exporter also listens on a UDP socket, port 9122 by default.

## Differences on this fork from the origin

For the purposes of clarity, the maintainers of the original package didn't feel this was of value so the PR was closed. Link is [here](https://github.com/prometheus/influxdb_exporter/pull/29).

This fork allows the user to change the Prometheus type (counter/gauge/untyped) through Influx line protocol tags. See below for a sample REST API call. Be sure to change the time within the default 5m window, or the time range chosen! Time is Unix time in nanoseconds and is the last field.

| *Note: When inserting the 'kind=x' tag, it does not affect the tag formation. The 'kind' tag key is purely used to set the Prometheus type and it's not subtractive in any way.*

```bash
curl -XPOST "http://localhost:9122/write" -d 'cpu,host=server02,kind=gauge,region=useast load=42 1532569830000000000'
```

## Alternatives

If you are sending data to InfluxDB in Graphite or Collectd formats, see the
[graphite_exporter][graphite_exporter]
and [collectd_exporter][collectd_exporter] respectively.

This exporter is useful for exporting metrics from existing collectd setups, as
well as for metrics which are not covered by the core Prometheus exporters such
as the [Node Exporter][node_exporter].

The exporter acts like an InfluxDB server, it does not connect to one. For
metrics concerning the InfluxDB server, use the [metrics endpoint][influxdb_metrics]
built into InfluxDB.

If you are already using Telegraf, it can serve the same purpose as this
exporter with the [`outputs.prometheus_client`][telegraf] plugin.

For more information on integrating between the Prometheus and InfluxDB
ecosystems, see the [influxdata integration page][influx_integration].

## Example usage with Telegraf

The influxdb_exporter appears as a normal InfluxDB server. To use with Telegraf
for example, put the following in your `telegraf.conf`:

```
[[outputs.influxdb]]
  urls = ["http://localhost:9122"]
```

Or if you want to use UDP instead:
```
[[outputs.influxdb]]
  urls = ["udp://localhost:9122"]
```

Note that Telegraf already supports outputing Prometheus metrics over HTTP via
[`outputs.prometheus_client`][telegraf], which avoids having to also run the influxdb_exporter.


[line_protocol]: https://docs.influxdata.com/influxdb/v0.10/write_protocols/line/
[graphite_exporter]: https://github.com/prometheus/graphite_exporter
[collectd_exporter]: https://github.com/prometheus/collectd_exporter
[node_exporter]: https://github.com/prometheus/node_exporter
[influxdb_metrics]: https://docs.influxdata.com/influxdb/v1.5/administration/server_monitoring/#influxdb-metrics-http-endpoint
[telegraf]: https://docs.influxdata.com/telegraf/v1.7/plugins/outputs/#prometheus-client-prometheus-client-https-github-com-influxdata-telegraf-tree-release-1-7-plugins-outputs-prometheus-client
[influx_integration]: https://www.influxdata.com/integration/prometheus-monitoring-tool/
