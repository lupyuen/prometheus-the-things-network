# Prometheus Configuration for The Things Network (MQTT)

To ingest MQTT Messages from The Things Network into Prometheus...

```bash
## Download mqtt2prometheus
go get github.com/hikhvar/mqtt2prometheus

## For macOS and Linux:
cd %GOPATH%/src/github.com/hikhvar/mqtt2prometheus

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
l{sensor="up",sensor_type="l",topic="v3/luppy-application@ttn/devices/eui-YOUR_DEVICE_EUI/up"} 4000 1634363600797
...
# HELP received_messages received messages per topic and status
# TYPE received_messages counter
received_messages{status="success",topic="v3/luppy-application@ttn/devices/eui-YOUR_DEVICE_EUI/up"} 3
...
# HELP t Temperature
# TYPE t gauge
t{sensor="up",sensor_type="t",topic="v3/luppy-application@ttn/devices/eui-YOUR_DEVICE_EUI/up"} 4952 1634363600797
```
