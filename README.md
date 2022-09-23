Repro for https://github.com/envoyproxy/envoy/issues/23234

## Run

```sh
docker-compose up
```

Observe no logs printed by collector using envoy 1.23.1:

```
upstream_1   | 127.0.0.1 - - [23/Sep/2022:23:36:54 +0000] "GET / HTTP/1.1" 200 14 "-" "curl/7.61.1" "-"
load_1       | Hello, World!
```

Change envoy-version _or_ opentelemetry-collector version in
docker-compose.yaml and observe logs (correctly) being printed by collector.
This is the expected result.

```
upstream_1   | 127.0.0.1 - - [23/Sep/2022:23:36:43 +0000] "GET / HTTP/1.1" 200 14 "-" "curl/7.61.1" "-"
load_1       | Hello, World!
collector_1  | 2022-09-23T23:36:44.277Z	info	LogsExporter	{"kind": "exporter", "data_type": "logs", "name": "logging", "#logs": 1}
collector_1  | 2022-09-23T23:36:44.277Z	info	ResourceLog #0
collector_1  | Resource SchemaURL:
collector_1  | Resource labels:
collector_1  |      -> log_name: STRING(ingress)
collector_1  |      -> zone_name: STRING()
collector_1  |      -> cluster_name: STRING()
collector_1  |      -> node_name: STRING()
collector_1  | ScopeLogs #0
collector_1  | ScopeLogs SchemaURL:
collector_1  | InstrumentationScope
collector_1  | LogRecord #0
collector_1  | ObservedTimestamp: 1970-01-01 00:00:00 +0000 UTC
collector_1  | Timestamp: 2022-09-23 23:36:43.856968 +0000 UTC
collector_1  | Severity:
collector_1  | Body: GET
collector_1  | Attributes:
collector_1  |      -> http.status_code: STRING(200)
collector_1  | Trace ID:
collector_1  | Span ID:
collector_1  | Flags: 0
collector_1  | 	{"kind": "exporter", "data_type": "logs", "name": "logging"}
```
