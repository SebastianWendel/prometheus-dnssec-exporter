# DNSSEC Exporter for Prometheus

Check for validity and expiration in DNSSEC signatures and expose metrics for Prometheus

## Installation

    $ go get -u github.com/chrj/prometheus-dnssec-exporter

## Usage

    Usage of prometheus-dnssec-exporter:
      -config string
        	Configuration file (default "/etc/dnssec-checks")
      -listen-address string
        	Prometheus metrics port (default ":9204")
      -resolver string
        	Resolver to use (default "8.8.8.8:53")
      -timeout duration
        	Timeout for network operations (default 10s)

## Metrics

### Gauge: `dnssec_zone_record_days_left`

Number of days the signature will be valid.

Labels:

* `zone`
* `record`
* `type`

### Gauge: `dnssec_zone_record_valid`

Does this record pass DNSSEC validation.

Labels:

* `zone`
* `record`
* `type`

### Examples

    # HELP dnssec_zone_record_days_left Number of days the signature will be valid
    # TYPE dnssec_zone_record_days_left gauge
    dnssec_zone_record_days_left{record="@",type="SOA",zone="ietf.org"} 357.5
    dnssec_zone_record_days_left{record="@",type="SOA",zone="verisigninc.com"} 11.333333333333334
    # HELP dnssec_zone_record_valid Does this record pass DNSSEC validation
    # TYPE dnssec_zone_record_valid gauge
    dnssec_zone_record_valid{record="@",type="SOA",zone="ietf.org"} 1
    dnssec_zone_record_valid{record="@",type="SOA",zone="verisigninc.com"} 1

## Configuration

Supply a configuration file path with `-config` (optionally, defaults to `/etc/dnssec-checks`). Uses [TOML](https://github.com/toml-lang/toml).

[Sample configuration file](config.sample)

## Prometheus target

Supply a listen address with `-addr` (optionally, defaults to `:9204`), and configure a Prometheus job:

    - job_name: "dnssec"
      scrape_interval: "1m"
      static_configs:
        - targets:
            - "server:9204"

## Prometheus alert

The real benefit is getting an alert triggered when a signature is nearing expiration or is not longer valid. Check this [sample alert definition](dnssec.rules).
