# Prometheus and Grafana for The Things Network (MQTT)

![Storing The Things Network Sensor Data with Prometheus](https://lupyuen.github.io/images/grafana-flow2.jpg)

We assume that the Payload Formatter for The Things Network has been configured...

https://github.com/lupyuen/cbor-the-things-network

# Ingest The Things Network MQTT Messages into Prometheus

To ingest MQTT Messages from The Things Network into Prometheus...

Download [`ttn-mqtt.yaml`](ttn-mqtt.yaml) and configure the MQTT Settings.

Download and run `mqtt2prometheus`...

```bash
## Download mqtt2prometheus
go get github.com/hikhvar/mqtt2prometheus

## For macOS and Linux:
cd $GOPATH/src/github.com/hikhvar/mqtt2prometheus

## For Windows:
cd %GOPATH%\src\github.com\hikhvar\mqtt2prometheus

## Build mqtt2prometheus
go build ./cmd

## Run mqtt2prometheus
go run ./cmd -log-level debug -config ttn-mqtt.yaml
```

We should see...

```text
mqttclient/mqttClient.go:20     
Connected to MQTT Broker

mqttclient/mqttClient.go:21     
Will subscribe to topic{"topic": "#"}

web/tls_config.go:191           
{"level": "info", "msg": "TLS is disabled.", "http2": false}

metrics/ingest.go:42    
Got message     
{"topic": "v3/luppy-application@ttn/devices/eui-YOUR_DEVICE_EUI/up", "payload": 
  {
    "end_device_ids": {
        "device_id": "eui-YOUR_DEVICE_EUI",
        "application_ids": {
            "application_id": "luppy-application"
        },
        "dev_eui": "YOUR_DEVICE_EUI",
        "join_eui": "0000000000000000",
        "dev_addr": "YOUR_DEVICE_ADDR"
    },
    "correlation_ids": [
        "as:up:01FJ3TF5MHDHK9ZP17DJRCVNCC",
        "gs:conn:01FJ36D90C9ETZFTFAB2N64JKM",
        "gs:up:host:01FJ36D98KBG2AKNYWE24GAVCD",
        "gs:uplink:01FJ3TF5DYDHZA5CY2YCXD05NG",
        "ns:uplink:01FJ3TF5E0D1HYPBQRRE0DYBB6",
        "rpc:/ttn.lorawan.v3.GsNs/HandleUplink:01FJ3TF5DZG9Q8RNF52TDP2TKX",
        "rpc:/ttn.lorawan.v3.NsAs/HandleUplink:01FJ3TF5MGSAFH6ANWXJK8R520"
    ],
    "received_at": "2021-10-16T05:51:19.698584300Z",
    "uplink_message": {
        "session_key_id": "YOUR_SESSION_KEY_ID",
        "f_port": 2,
        "f_cnt": 18,
        "frm_payload": "omF0GROZYWwZD6A=",
        "decoded_payload": {
            "l": 4000,
            "t": 5017
        },
        "rx_metadata": [
            {
                "gateway_ids": {
                    "gateway_id": "YOUR_GATEWAY_ID",
                    "eui": "YOUR_GATEWAY_EUI"
                },
                "time": "2021-10-16T05:58:01.085341Z",
                "timestamp": 3854967529,
                "rssi": -55,
                "channel_rssi": -55,
                "snr": 13.2,
                "location": {
                    "latitude": 1.27125,
                    "longitude": 103.80795,
                    "altitude": 70,
                    "source": "SOURCE_REGISTRY"
                },
                "uplink_token": "YOUR_UPLINK_TOKEN",
                "channel_index": 4
            }
        ],
        "settings": {
            "data_rate": {
                "lora": {
                    "bandwidth": 125000,
                    "spreading_factor": 10
                }
            },
            "data_rate_index": 2,
            "coding_rate": "4/5",
            "frequency": "922600000",
            "timestamp": 3854967529,
            "time": "2021-10-16T05:58:01.085341Z"
        },
        "received_at": "2021-10-16T05:51:19.488029108Z",
        "consumed_airtime": "0.370688s",
        "network_ids": {
            "net_id": "000013",
            "tenant_id": "ttn",
            "cluster_id": "ttn-au1"
        }
    }
  }
}
```

To check the ingested metrics...

```bash
curl -v http://localhost:9641/metrics
```

We should see...

```text
# HELP l Light Level
# TYPE l gauge
l{sensor="eui-YOUR_DEVICE_EUI",sensor_type="l",topic="v3/luppy-application@ttn/devices/eui-YOUR_DEVICE_EUI/up"} 4000 1634364863274
...
# HELP received_messages received messages per topic and status
# TYPE received_messages counter
received_messages{status="success",topic="v3/luppy-application@ttn/devices/eui-YOUR_DEVICE_EUI/up"} 3
...
# HELP t Temperature
# TYPE t gauge
t{sensor="eui-YOUR_DEVICE_EUI",sensor_type="t",topic="v3/luppy-application@ttn/devices/eui-YOUR_DEVICE_EUI/up"} 5056 1634364863274
```

# Configure Prometheus

To add the ingested metrics to Prometheus, edit `prometheus.yml`...

```yaml
scrape_configs:
  ...

  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "ttn"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9641"]
```

To see the ingested metrics in Prometheus, browse to...

http://localhost:9090/

In the query box enter...

```text
t
```

Or...

```text
l
```

Click `Execute → Graph`

We should see a graph of the Temperature or Light Level...

![Prometheus Graph](https://lupyuen.github.io/images/prometheus-metric.png)

# Configure Grafana

To add the Prometheus metrics to Grafana, add a Prometheus Data Source with the URL...

```text
http://localhost:9090
```

![Prometheus Data Source for Grafana](https://lupyuen.github.io/images/prometheus-grafana2.png)

Add a Grafana Panel with the Prometheus Data Source.

Set the Metric to `t` or `l`...

![Prometheus Panel for Grafana](https://lupyuen.github.io/images/prometheus-grafana3.png)

The Grafana chart appears...

![Prometheus Chart in Grafana](https://lupyuen.github.io/images/prometheus-grafana.png)
